# i茅台 API 客户端

## 背景与目标
封装 i茅台 APP 的 HTTP API，提供统一的请求入口，处理签名、认证、重试等通用逻辑，让上层业务代码无需关心底层细节。

## 设计方案
- 数据模型：无持久化，请求/响应使用 dataclass 或 dict
- API 接口定义：
  - `send_code(mobile)` — 发送验证码
  - `login(mobile, code, device_id)` → token, user_id
  - `get_items()` → 商品列表
  - `get_shops(city_code)` → 门店列表
  - `reservation(token, item_id, shop_id)` → 申购结果
  - `travel(token)` → 旅行（维护耐力值）
- 业务流程：所有请求统一走 `ApiClient._request()`，内含签名注入、重试、错误处理
- 依赖关系：依赖 `utils/logger.py`、`utils/signer.py`（签名工具）

## 边界与约束
- 性能要求：申购接口必须在 09:00:00 ± 500ms 内完成发出
- 安全考量：token/device_id 只从配置读取，不落日志
- 已知限制：签名算法随 APP 版本更新，需持续维护

## Todo-List
- [x] 实现 `utils/signer.py` 签名算法
- [x] 实现 `api/client.py` 基础请求封装（含重试、日志）
- [x] 实现登录接口
- [x] 实现申购接口
- [x] 实现旅行接口
- [x] 实现商品/门店查询接口
- [x] 单元测试（mock 网络请求）
- [x] 文档更新
