# Развёртывание в Kubernetes

## Ресурсы

- `ff-crud`: `Deployment` + `ClusterIP Service :8000`; внутри также живёт web.
- `ff-evaluator`: `Deployment` + `ClusterIP Service :8000`; масштабируется независимо.
- `ff-distributor`: `Deployment` без `Service`; строго одна реплика.
- `MongoDB`: `StatefulSet` + `Service` + `PVC`
- `oauth2-proxy`: `Deployment` + `ClusterIP Service :4180`

Изменение числа реплик `ff-crud` и `ff-evaluator` не меняет схему: состояние не хранится в pod, а Service балансирует запросы.

## Путь запроса

- `https://<domain>/userapi/evaluate` → Ingress → `ff-evaluator:8000/evaluate` (публично, rate limit);
- `https://<domain>/api/flags/*` → Ingress → OAuth2 → `ff-crud:8000` (административный api);
- `https://<domain>/{/,flags,assets/*}` → Ingress → OAuth2 → `ff-crud:8000` (web-админка);
- `https://<domain>/oauth2/*` → Ingress → `oauth2-proxy:4180`.

## ConfigMap и Secret

**ConfigMap**

- адрес и порт каждого сервиса;
- имя базы MongoDB;
- адрес `ff-crud`, интервалы опроса и число сохраняемых снапшотов;
- внешний домен и OAuth redirect url.

**Secret**

- адрес подключения, логины, пароли и replica-set key MongoDB;
- OAuth client id и client secret;
- токены доступа к object storage и registry.

## Состояние

- Флаги → MongoDB; владелец — `ff-crud`.
- Снапшоты → object storage; владелец — `ff-distributor`.
- Активный снапшот → память каждого `ff-evaluator`; восстанавливается из снапшота, здесь состояние оставлено в памяти намеренно ради производительности.
- `Idempotency-Key` → Redis; владелец — `ff-crud`.

## Пробы

- `ff-crud` readiness: `/health` возвращает ok только при доступной MongoDB.
- `ff-evaluator` readiness: `/health` возвращает ok только после загрузки в память валидного снапшота.
- `ff-distributor` readiness: `/health` возвращает ok после первой публикации.
- `oauth2-proxy` readiness: встроенный `/ready`.
- `MongoDB` readiness: `ping` и готовность replica set принимать запросы.
