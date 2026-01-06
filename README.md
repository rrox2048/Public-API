# Public-API — 视频解析下载开放 API 接口

🎯 稳定解析 / 高清下载 / 解除地区限制 / 移动端可保存

适合场景：视频解析与下载类网站或服务。

## 一句话说明

当平台更新导致解析脚本失效、用户下载失败或视频质量/格式异常时，我们提供稳定的后端解析与下载能力，帮你省去维护成本并提升用户体验。

## 你可能遇到的问题

- 平台更新 → 自己维护的解析脚本失效、无人维护。
- 只能拿到 360p 以下低分辨率，720p/1080p/4K 没声音或无音轨。
- 拿到真实地址但用户下载失败（地区限制 / 403）。
- 视频模糊不清晰 / 格式不对无法在客户端保存。
- 手机端下载后无法保存或播放。

以上大多数不是前端问题，而是后处理与网络（代理、跨域、HTTP 状态）相关的问题。

## 我们提供什么

### 1) 基础解析（免费 API / 长期免费稳定提供）

- 支持 1000+ 海内外主流平台（YouTube、Instagram、Twitter、Facebook、TikTok、Bilibili 等）。
- 平台更新由我们维护，保证解析稳定性。
- 返回真实播放/下载地址，解决“能解析但无法下载”（地区限制、403 等）的问题。

适合：只想要稳定解析、不想长期维护脚本的站长。

在线测试：你可以在 https://api.videofetcher.net/ 进行测试（免费的，直接测就行，测试又不需要付出什么）。

---

### 2) 进阶解析合作（付费/合作）

- 修复无声音 / 音轨丢失问题。
- 解决移动端保存兼容性（格式/容器转换）。
- 提升画质或修复模糊问题（针对部分平台的视频源）。
- 支持 2K/4K 视频下载与保存。

适合：用户投诉多、规模化站点或“解析成功但无法下载”的服务。

---

### 3) 高级解析（私人定制）

- 私人定制方案，按需优化解析、代理与后处理链路。请直接联系网站客服或在接入后通过商务渠道洽谈。


## API 文档（基础信息）

- API 请求域名：https://api.obtaindown.com
- 请求格式：JSON
- 字符编码：UTF-8
- 认证方式：API Key（通过请求头传递）

认证说明

所有接口需要在请求头中携带您的 API Key：

- key: YOUR_API_KEY


## 接口列表

### 视频解析

- 方法：POST
- 路径：/obApi/api/analysis
- 描述：视频解析接口，返回下载/播放地址信息
- 认证：需要（在请求头中携带 key）

请求参数

| 参数名 | 类型   | 必填 | 说明                 |
|--------|--------|------|----------------------|
| url    | String | 是   | 待解析的视频/链接 URL |

响应示例（无分辨率选择）

```json
{
  "headers": {
    "cookie": "fhdshfkjdshfjkdsa"
  },
  "re_url": "https://www.instagram.com/reel/DSUpKIdk7p-",
  "title": "",
  "proxyInfo": {
    "ip": "89.185.27.9",

  },
  "upload_date": "",
  "uploader": "",
  "url_list": [
    {
      "acodec": "",
      "audio": "https://scontent-tpe1-1.cdninstagram.com/o1/v/t16/f2/m69/AQOK...",
      "ext": "mp4",
      "filesize": 11897550,
      "url": "https://scontent-tpe1-1.cdninstagram.com/o1/v/t2/f2/m367/AQOW...",
      "vcodec": "vp09.00.40.08.00.01.01.01.00",
      "videoOnly": true
    }
  ],
  "with_resolution": false
}
```

> 有分辨率选择的响应示例与结构类似，url_list 中会包含不同分辨率的条目。

响应字段说明

主字段说明

| 字段名      | 类型    | 必有 | 说明                                                                 |
|-------------|---------|------|----------------------------------------------------------------------|
| headers     | Object  | 否   | 请求头信息，可能包含 cookie 等数据                                    |
| re_url      | String  | 是   | 原始视频 URL                                                          |
| title       | String  | 否   | 视频标题                                                              |
| upload_date | String  | 否   | 上传日期                                                              |
| uploader    | String  | 否   | 上传者名称                                                            |
| url_list    | Array   | 是   | 视频下载列表（数组）                                                   |
| with_resolution | Boolean | 是 | 是否支持分辨率选择（true/false）                                      |

url_list 数组项字段说明

| 字段名     | 类型    | 必有 | 说明                                |
|------------|---------|------|-------------------------------------|
| url        | String  | 是   | 视频下载地址                         |
| audio      | String  | 否   | 音频下载地址（若单独提供）           |
| ext        | String  | 是   | 文件扩展名（如 mp4）                |
| filesize   | Number  | 否   | 文件大小（字节）                    |
| vcodec     | String  | 否   | 视频编码格式                        |
| acodec     | String  | 否   | 音频编码格式                        |
| videoOnly  | Boolean | 否   | 是否为纯视频（无音频）              |

错误码说明

| 错误码   | 说明                          |
|----------|-------------------------------|
| 401      | 请求头没有 key                |
| 403      | apiKey 不存在或无权限         |
| 402      | 请求的 IP 与 API KEY 对应的 IP 有误 |
| 429      | 解析请求频率限制              |
| 10001    | 解析次数不足                  |
| 10002    | 解析失败                      |


## SDK / 使用示例

JavaScript（Fetch）

```javascript
fetch('https://api.obtaindown.com/obApi/api/analysis', {
  method: 'POST',
  headers: {
    'key': 'YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    url: 'https://www.youtube.com/watch?v=xxx'
  })
})
.then(res => res.json())
.then(data => console.log(data))
.catch(err => console.error(err))
```

Python（requests）

```python
import requests

headers = {
    'key': 'YOUR_API_KEY',
    'Content-Type': 'application/json'
}

data = {
    'url': 'https://www.youtube.com/watch?v=xxx'
}

resp = requests.post(
    'https://api.obtaindown.com/obApi/api/analysis',
    headers=headers,
    json=data
)

print(resp.json())
```

cURL

```bash
curl -X POST 'https://api.obtaindown.com/obApi/api/analysis' \
  -H 'key: YOUR_API_KEY' \
  -H 'Content-Type: application/json' \
  -d '{"url":"https://www.youtube.com/watch?v=xxx"}'
```


## 常见问题与建议

- 如果返回的 proxyInfo 为空或下载失败，建议更换代理或检查目标资源的地区限制。
- 若遇到无声音、格式或兼容性问题，可升级到进阶或高级合作以获得更深入的后处理支持。


## 联系方式

如需进阶/定制服务或商业合作，请通过项目主页或仓库 issue 联系我们。
api网站地址：https://api.videofetcher.net


---

以上为要写入 README.md 的完整内容。
