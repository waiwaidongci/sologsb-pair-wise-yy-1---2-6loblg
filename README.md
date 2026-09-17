# 机械钟表擒纵调校API

纯后端零依赖Node服务，使用 `data/db.json` 持久化钟表档案、调校记录和复测记录。

## 启动

```bash
PORT=3021 node server.js
```

## 主要接口

- `GET /health`
- `GET /clocks`
- `POST /clocks`
- `GET /clocks/not-qualified`
- `GET /clocks/:id/history`
- `POST /clocks/:id/adjustments`
- `POST /clocks/:id/retests`
- `GET /clocks/:id/latest-retest`
- `GET /adjustments?clockId=`
- `GET /retests?clockId=&qualified=`

## 下一步处置

`GET /clocks` 列表与 `GET /clocks/:id/history` 单表详情共用同一判断，在钟表对象上返回 `nextAction`：

- `停调`：最新一次复测已达标，较早的未达标记录不覆盖该结论
- `需保养`：最新一次复测未达标且振幅低于 220
- `继续微调`：最新一次复测未达标且振幅不低于 220
- `待复测`：已有调校依据但尚无复测
- `待首次调校`：找不到调校依据且尚无复测

## 闭环示例

```bash
curl http://127.0.0.1:3021/clocks/not-qualified
curl -X POST http://127.0.0.1:3021/clocks/clock_demo/retests \
  -H 'Content-Type: application/json' \
  -d '{"dailyRateSeconds":12,"amplitude":252,"note":"复测进入目标范围"}'
```
