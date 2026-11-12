# Agents・会話・成果物API

JAPAN AI Studioの公開APIで、Agent、会話room、Canvas成果物を扱います。実行は`/chat/v2`を使用します。SDKは[SDK Agents：実行・ストリーム](../development/sdk-agents-invoke-and-stream.md)と[SDK Agents：会話履歴・Canvas](../development/sdk-agents-history-and-canvas.md)を参照してください。

## 関連記事

- [SDK Agents：実行・ストリーム](../development/sdk-agents-invoke-and-stream.md)
- [SDK Agents：会話履歴・Canvas](../development/sdk-agents-history-and-canvas.md)

## この記事の範囲

本番APIベースURLは`https://api.japan-ai.co.jp`です。Project APIキーは`x-api-key`または`Authorization: Bearer <key>`で渡し、サーバー側だけに保管します。読取には`agent:read`、room作成とProject APIキーによる実行には`agent:execute`が必要です。

> Project APIキーは作成者として扱われます。共有キーで利用者別のprivateな会話閲覧はできません。ブラウザではAPIキーを使わず、SDKのPage実行環境の認証経路を使用してください。

```sh
export JAI_STUDIO_API_KEY='pak_...'
export JAI_API='https://api.japan-ai.co.jp'
```

## 1. Agentを検索・取得する

| メソッド・パス | 必要スコープ | パラメーター | 主な成功応答 | 状態 |
|---|---|---|---|---|
| `GET /api/v1/agents` | `agent:read` | `limit`：任意、1〜100、既定50／`cursor`：任意／`search`：任意 | `agents`、`totalCount`、`nextCursor` | 200、400、401、403 |
| `GET /api/v1/agents/{agentId}` | `agent:read` | `agentId`：パス、必須 | `agent` | 200、401、403、404 |

`search`はname、label、descriptionを対象にします。`agent`には`id`、`name`、`label`、`description`、`icon`、`enabled`、`createdAt`、`updatedAt`が含まれます。`cursor`は応答の`nextCursor`をそのまま渡します。

```sh
curl -sS "$JAI_API/api/v1/agents?limit=50&search=%E5%96%B6%E6%A5%AD" \
  -H "x-api-key: $JAI_STUDIO_API_KEY"
```

## 2. Agent定義を作成・更新する

Project APIキーでは、一覧・詳細取得とは別に、Agent定義の作成・取得・更新を行えます。いずれも`agent:write`が必要で、キーの作成者にも対象Agentを作成・更新する権限が必要です。

| 操作 | メソッド・パス | 主な本文・応答 |
|---|---|---|
| 作成 | `POST /api/v1/agents` | 必須`name`、`prompt`、`model`。任意で`description`、`openingStatement`、`modelMode`、`datasets`、`tools`、`maxOutputTokens`、`reasoningEffort`、`temperature`。201。 |
| 定義取得 | `GET /api/v1/agents/{agentId}/definition` | 更新に渡せる定義を返します。 |
| 更新 | `PATCH /api/v1/agents/{agentId}` | 指定しない項目は保持します。配列は全置換し、`null`は対応する項目を解除します。 |

`datasets`と`tools`は最大200件です。AgentにDatasetを関連付ける前に、対象ProjectのDatasetと利用可否を確認します。定義の読取りにも`agent:write`が必要であるため、単に会話を読む権限の代替にはしません。

```sh
curl -sS -X POST "$JAI_API/api/v1/agents" \
  -H "x-api-key: $JAI_STUDIO_API_KEY" -H 'Content-Type: application/json' \
  -d '{"name":"提案整理（検証用）","prompt":"入力を3点で整理してください","model":"利用可能なモデル名"}'
```

## 3. roomを作成して同期実行する

会話を先に作成する場合は、次を呼び出します。

| メソッド・パス | 必要スコープ | パラメーター | 主な成功応答 | 状態 |
|---|---|---|---|---|
| `POST /api/v1/agents/{agentId}/rooms` | `agent:execute` | `agentId`：パス、必須 | `roomId`、`agentId`、`createdAt` | 201、401、403、404 |

返された`roomId`を`sessionId`として渡すと会話を継続できます。同期実行は`POST /chat/v2`です。`agentName`にはAgent UUIDを渡せます。`prompt`は必須で、`stream: false`を指定します。

| メソッド・パス | 必要スコープ | 主な本文パラメーター | 主な成功応答 | 状態 |
|---|---|---|---|---|
| `POST /chat/v2` | `agent:execute` | `prompt`：必須。`agentName`、`stream`、`sessionId`、`model`、`temperature`、`systemPrompt`、`skipHistory`、`is_ephemeral`、`enable_handoff_long_run_job`は任意 | `chatMessage`、`sessionId`。必要に応じて`references`、画像・ファイル情報 | 201、400、401、403、409、429、500 |

```sh
curl -sS -X POST "$JAI_API/chat/v2" \
  -H "x-api-key: $JAI_STUDIO_API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{"agentName":"550e8400-e29b-41d4-a716-446655440000","prompt":"提案を3点で示してください","stream":false}'
```

`sessionId`は応答内容とエラーを確認してから利用します。`is_ephemeral: true`の実行は履歴読取や会話継続を前提にできません。

### 添付ファイル

添付時は`multipart/form-data`で`files`へ送信します。本文項目も同じフォームへ入れます。1要求は最大10ファイル、1ファイルは最大100MBです。未検証の内容をそのまま表示・実行しないでください。

## 4. SSEで出力を受け取る

ストリームも`POST /chat/v2`を使用します。本文の`stream`を`true`にし、`Accept: text/event-stream`を指定します。成功時はHTTP 200、`Content-Type: text/event-stream`です。SSEの`data:`行を順に処理し、`data: [DONE]`で終端を判定します。`delta`が空でも画像、ファイル、`finishReason`を持つ場合があるため捨てません。`type: "error"`を受けた場合は本文を完了結果として確定しません。409は同じ`requestId`が処理中の場合に返り得るため、確認せずに再送しません。

## 5. roomの履歴とCanvas成果物を読む

| メソッド・パス | 必要スコープ | パラメーター | 主な成功応答 | 状態 |
|---|---|---|---|---|
| `GET /api/v1/agents/rooms/{roomId}/messages` | `agent:read` | `roomId`：パス、必須／`limit`：任意、1〜100、既定50／`cursor`：任意 | `roomId`、`messages`、`totalCount`、`nextCursor`、`state` | 200、400、401、403、404 |
| `GET /api/v1/agents/rooms/{roomId}/canvas-artifacts` | `agent:read` | `roomId`：パス、必須／`limit`：任意、1〜100、既定50／`cursor`：任意 | `roomId`、`canvasArtifacts`、`nextCursor` | 200、400、401、403、404 |
| `GET /api/v1/agents/rooms/{roomId}/canvas-artifacts/{canvasArtifactId}` | `agent:read` | `roomId`、`canvasArtifactId`：パス、必須 | `canvasArtifact` | 200、401、403、404 |
| `PUT /api/v1/agents/rooms/{roomId}/canvas-artifacts/{canvasArtifactId}` | `agent:execute` | `roomId`、`canvasArtifactId`：パス、必須／本文：`name`、`content` | 作成または置換したCanvas | 200、400、403、404、409、413 |
| `PUT /api/v1/agents/rooms/{roomId}/canvas-artifacts/{canvasArtifactId}/publication/{scope}` | `agent:execute` | `roomId`、`canvasArtifactId`、`scope`：パス、必須 | 公開結果 | 200、403、404、409 |
| `DELETE /api/v1/agents/rooms/{roomId}/canvas-artifacts/{canvasArtifactId}/publication` | `agent:execute` | `roomId`、`canvasArtifactId`：パス、必須 | 公開停止の結果 | 200、403、404 |

メッセージは`id`、`role`、`content`、`createdAt`、`feedback`を含みます。ページング中に新しいメッセージが追加され得るため、`id`で重複を除外します。roomが対象Projectから読めない場合も404になり得ます。

Canvas一覧は本文を返さず、`id`、`name`、`documentType`、`contentSizeBytes`、作成・更新日時を返します。必要なものだけ個別取得します。個別応答の`content`は保存時のJSONで、旧形式では`documentType`が`null`かつ`{ "text": ... }`の場合があります。HTMLスライドの`content.text`はJSON文字列で、HTMLや`room://`参照はURLへ解決されません。

> CanvasのJSON・HTML・添付由来の内容を検証せずに`innerHTML`へ設定してはいけません。サニタイズ、隔離、Content Security Policyを実装してください。

### SDK 0.11.0でCanvasへ書き込み・公開する

SDK 0.11.0では、Pageのサインイン済み利用者が、公開API経由で作成したroomのCanvasへhslides形式のデッキを書込み・公開できます。`putCanvasArtifact(roomId, canvasArtifactId, { name, content })`は指定したCanvas IDで作成または置換します。`content`はHTML文字列そのものではなく、`{ document_type: "hslides", text: "..." }`形式です。結果が不明な書込みは、同じIDで状態を確認してから扱います。これらの書込み・公開は、Pageのサインイン済み利用者向けです。Project APIキーなど利用者を特定できない認証では403になり、対応していないデプロイでは404になります。

公開は`publishCanvasArtifact(roomId, canvasArtifactId, scope)`を使い、`scope`は`"PUBLIC"`または`"ORGANIZATION"`です。`PUBLIC`は組織で公開共有が許可されていないと拒否され、`ORGANIZATION`は公開roomでは拒否されます。`unpublishCanvasArtifact()`はroomの共有範囲へ戻します。SDKとPage実行環境の対応バージョンをそろえ、Canvas本文・リンク・添付に秘密情報を含めません。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 400になる | 必須項目、`limit`の範囲、JSONまたはmultipartの形式を確認します。要求を推測して自動修正・再送しません。 |
| 401または403になる | キーの保管先・有効性、対象Project、読取または実行スコープを確認します。キーの実値をログや問い合わせへ含めません。 |
| 404になる | Agent ID、room ID、Canvas ID、対象Project、認証経路を確認します。別のIDを試して存在を探索しません。 |
| 429になる | `retryAfterSeconds`と`Retry-After`があればそれに従い、並列数と再送頻度を下げます。固定の待機秒数を実装しません。 |
| ストリームが途切れる、またはタイムアウトする | 完了・未実行を画面だけで判断せず、room履歴と業務上の結果を確認してから再開します。 |

429では`retryAfterSeconds`が返る場合があります。再試行と利用制御は[API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)を参照してください。

## チェックリスト

- [ ] APIキーをサーバー側だけに保管し、最小スコープを選んだ
- [ ] Agent定義を変更する場合は`agent:write`とキー作成者の権限、配列全置換・`null`解除の意味を確認した
- [ ] Agent IDでroom作成または実行を行った
- [ ] 同期・SSE・HTTPエラーを分けて処理した
- [ ] `nextCursor`を引き継ぎ、`limit`を1〜100にした
- [ ] Canvasはroom IDとCanvas IDの組で取得した
- [ ] Canvasを書込み・公開する場合は、SDK 0.11.0、対応するPage実行環境、公開範囲を確認した
- [ ] 添付、Canvas、Agent出力を未検証のまま表示・実行していない
- [ ] 429とタイムアウト後に自動再送していない

## 次に読む記事

- [SDK Agents：実行・ストリーム](../development/sdk-agents-invoke-and-stream.md)
- [SDK Agents：会話履歴・Canvas](../development/sdk-agents-history-and-canvas.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
