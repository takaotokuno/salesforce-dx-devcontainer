## English

### Troubleshooting browser login in a Dev Container

When you run:

```bash
sf org login web --set-default-dev-hub --alias DevHub
```

the Salesforce CLI starts a temporary OAuth callback server on port `1717`.
After authentication, Salesforce redirects the browser to:

```text
http://localhost:1717/OauthRedirect
```

In some Dev Container environments, the Salesforce CLI listens only on the
IPv6 loopback address:

```text
[::1]:1717
```

while the VS Code port forward connects to the container through the IPv4
loopback address:

```text
127.0.0.1:1717
```

In that case, authentication succeeds in Salesforce, but the result does not
reach the CLI and the login command eventually times out.

Check the active listener from another terminal while the login command is
waiting:

```bash
ss -ltnp | grep 1717
```

If the output shows only `[::1]:1717`, verify the difference directly:

```bash
curl -v 'http://[::1]:1717/OauthRedirect'
curl -v http://127.0.0.1:1717/OauthRedirect
```

A `404 Resource not found` response from `[::1]` is sufficient to confirm that
the OAuth server is reachable. The test request does not contain an OAuth
authorization code, so a successful connection does not necessarily return a
successful HTTP status.

To forward the IPv4 callback to the IPv6 listener, run the following command in
a second container terminal:

```bash
sudo socat \
  TCP4-LISTEN:1717,bind=127.0.0.1,reuseaddr,fork \
  TCP6-CONNECT:[::1]:1717
```

Use the following order:

1. Run `sf org login web`.
2. Wait until the CLI is listening on `[::1]:1717`.
3. Start the `socat` command in another terminal.
4. Complete the Salesforce login in the browser.

Do not start `socat` before `sf org login web`. The Salesforce CLI can report
`PortInUseError` if port `1717` is already in use when it starts its OAuth
redirect server.

After starting `socat`, both listeners should be visible:

```bash
ss -ltnp | grep 1717
```

Expected state:

```text
127.0.0.1:1717  socat
[::1]:1717      Salesforce CLI
```

You can then verify the IPv4 path:

```bash
curl -v http://127.0.0.1:1717/OauthRedirect
```

If an HTTP response is returned, the callback path is available:

```text
Browser
→ host localhost:1717
→ VS Code port forwarding
→ container 127.0.0.1:1717
→ socat
→ container [::1]:1717
→ Salesforce CLI
```

This workaround is only required when the CLI and the Dev Container port
forward use different IP address families.

---

## 日本語

### Dev Containerでブラウザーログインが完了しない場合

次のコマンドを実行すると、Salesforce CLIはOAuthコールバックを受け取るため、
一時的なHTTPサーバーを1717番ポートで起動します。

```bash
sf org login web --set-default-dev-hub --alias DevHub
```

Salesforceでの認証後、ブラウザーは次のURLへリダイレクトされます。

```text
http://localhost:1717/OauthRedirect
```

Dev Containerの環境によっては、Salesforce CLIがIPv6のループバックアドレスだけで
待ち受ける場合があります。

```text
[::1]:1717
```

一方、VS Codeのポート転送がコンテナー内のIPv4ループバックへ接続すると、
接続先は次になります。

```text
127.0.0.1:1717
```

この状態では、Salesforce上のログインには成功しても、OAuthの結果がCLIまで届かず、
最終的にログインコマンドがタイムアウトします。

ログインコマンドが待機している間に別のターミナルを開き、待受け状態を確認します。

```bash
ss -ltnp | grep 1717
```

`[::1]:1717`だけが表示される場合は、IPv6とIPv4の接続状態を確認します。

```bash
curl -v 'http://[::1]:1717/OauthRedirect'
curl -v http://127.0.0.1:1717/OauthRedirect
```

`[::1]`への接続で`404 Resource not found`が返る場合でも、OAuthサーバーへの
接続自体は成功しています。確認用リクエストにはOAuthの認可コードが含まれないため、
HTTPステータスが成功になる必要はありません。

IPv4側のコールバックをIPv6側のSalesforce CLIへ中継するには、別のコンテナー内
ターミナルで次を実行します。

```bash
sudo socat \
  TCP4-LISTEN:1717,bind=127.0.0.1,reuseaddr,fork \
  TCP6-CONNECT:[::1]:1717
```

実行順序は次のとおりです。

1. `sf org login web`を実行します。
2. CLIが`[::1]:1717`で待受けを開始するまで待ちます。
3. 別のターミナルで`socat`を起動します。
4. ブラウザー上でSalesforceへのログインを完了します。

`sf org login web`より先に`socat`を起動しないでください。Salesforce CLIが
OAuthサーバーを起動するときに1717番ポートが使用中だと、次のエラーになる場合があります。

```text
Error (PortInUseError): Cannot start the OAuth redirect server on port 1717.
```

`socat`の起動後は、IPv4とIPv6の両方で待受けていることを確認できます。

```bash
ss -ltnp | grep 1717
```

期待する状態は次のとおりです。

```text
127.0.0.1:1717  socat
[::1]:1717      Salesforce CLI
```

IPv4側からの疎通も確認できます。

```bash
curl -v http://127.0.0.1:1717/OauthRedirect
```

HTTPレスポンスが返れば、次のコールバック経路が成立しています。

```text
ブラウザー
→ ホストのlocalhost:1717
→ VS Codeのポート転送
→ コンテナーの127.0.0.1:1717
→ socat
→ コンテナーの[::1]:1717
→ Salesforce CLI
```

この対応は、Salesforce CLIの待受けとDev Containerのポート転送で、
IPv4とIPv6が一致しない場合にのみ必要です。
