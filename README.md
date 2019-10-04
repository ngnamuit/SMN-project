# SMN-project

## I. Mục đích

Do nhu cầu intergate giữa hệ thống odoo với các đối tác ngoài càng tăng nên cần 1 restapi riêng biệt nằm ngoài hệ thống odoo hiện tại để đảm đáp ứng các nhu cầu sau:
 - Dễ dàng quản lý, maintaince, phát triển.
 - Deploy nhanh, tiện lơi, không ảnh hưởng tới hệ thống vận hành odoo.
 - Có logs, raise logs khi có vấn đề xảy ra.
 - Giảm risks, tăng performance cho hệ thống vận hành odoo.
 - Đảm bảo hệ thống luôn available  kể cả khi odoo deploy sẽ không bị ảnh hưởng.
 - Đáp ứng được transactions (in/out) lớn.
 - Có UI manages các endpoint api, control, build beauty endpoints.
 - Cấp phát token chứng thực (JWT) dễ dàng, độ phức tap cao và dễ dàng thu hồi để bảo hệ thống bảo mật tốt.

Demo : http://sample-python-api-s.herokuapp.com/

Source : https://github.com/ngnamuit/sample-python-api



## II. So sánh GUNICORN WSGI vs KONG

### 1.GUNICORN

 - Guinicorn là Python Web Server Gateway Interface
 - Sử dụng gunicorn & flask đáp ứng đủ yêu cầu sau:
    - Tương thích với python, cấu hình đơn giản.
    - RestAPIs xử lý được transactions lớn, độc lập.
    - Có UI manage endpoints, document cho enpoints.
    - Có JWT token để cấp phát sign và thu hồi dễ dàng.
    - Dễ dàng mở rộng, phát triển, quản lý.
    - Độ phổ biến cao, được recomment sử dụng với flask framework, có nhân lực có kinh nghiệm nên build và phát triển nhanh.
    - Cộng đồng sử dụng lớn.
 - Infracstructure recommadation: 
    - CPU: 4 core  RAM 8GB 
    - 4 core CPU chạy cho ($CPU_CORES * 2) + 1 = 9worker có thể xử lử ~400k/ngày
    - Sử dụng gunicorn start flask có thể dễ dàng upgrade worker bằng 1 command line.

### 2. KONG

- Kong là 1 api gateway hay 1 api middelware  recomment khi sử dụng microservices.
- Các tính năng kong match với nhu cầu hiện tại như:
    + Có UI manage endpoints, document cho enpoints (Restful Administrator API).
    + Dễ dàng mở rộng và quản lý.
    + Có JWT token để cấp phát sign (cơ bản) và thu hồi sign (bản enteprise có phí).
- Hạn chế khi sử dụng kong:
    + Chưa có nhân sự có kinh nghiệm sử dụng kong, tốn nhiều thời gian research để phát triển và control.
    + Cần 1 database riêng cho kong.

## III. Technical improvement for REST.

### 1. Sử dụng X-Request-Id (uuid python) để tracking log với lỗi H13 (heroku).

- Add resquest_id vào request header -> using middelware.
- Falcon structure example: https://github.com/jmvrbanac/falcon-example
```
import uuid


def set_context(req, resp):
    if not req.context.get('request_id'):
            req.context['request_id'] = str(uuid.uuid4())
    
        resp.set_header('request-id', req.context['request_id'])


class ContextMiddleware(object):
    def process_request(self, req, resp):
            set_context(req, resp)

```
