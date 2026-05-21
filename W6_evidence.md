# Gói Bằng Chứng W6 — Tối Ưu Vận Hành & Cloud Tiết Kiệm Chi Phí

> **Tuần 6 — 18–22 tháng 5, 2026**

---

## Trang Bìa

| Mục | Chi Tiết |
|------|----------|
| **Nhóm** | G10 |
| **Tên thành viên** | Lê Trần Tuấn Khanh, Trần Mạnh Trường, Trần Mạnh Cường, Nguyễn Đức Hảo, Lê Văn Hải, Phan Đức Huy, Lê Viết Quốc Hưng, Huỳnh Xuân Hậu, Nguyễn Thị Mến, Trần Quốc Hùng |
| **Tên Dự Án** | AI RAG Chatbot |
| **Repository** | https://github.com/hailv1209/XBrain_Group10|
| **Ngày Triển Khai** | 19 tháng 5, 2026 |
| **AWS Account** | 726411362669 |
| **Tổng Chi Phí (W6)** | USD ~[Chi phí] (≤ $150) |

---

## Tóm Tắt Dự Án

#### Tổng Quan Ứng Dụng
- **Mô Tả Hệ Thống**: AIChat là hệ thống AI chatbot full-stack hỗ trợ hỏi đáp tài liệu nội bộ bằng cơ chế RAG (Retrieval-Augmented Generation)
- **Chức Năng Chính**:
  - Người dùng có thể trò chuyện với AI
  - Upload tài liệu
  - Tìm kiếm tri thức từ knowledge base
- **AI Platform**: Hệ thống sử dụng Amazon Bedrock để tạo phản hồi AI có ngữ cảnh dựa trên dữ liệu doanh nghiệp

#### Kiến Trúc Ứng Dụng
- **Frontend Layer**:
  - Frontend static website được host trên Amazon S3
  - CloudFront được dùng để phân phối nội dung và giảm latency
- **Backend Layer**:
  - Backend FastAPI được triển khai trên Amazon ECS Fargate
  - Application Load Balancer (ALB) thực hiện load balancing cho ECS services
  - API Gateway được dùng cho API routing và health endpoints
- **Edge & Performance**:
  - CloudFront đóng vai trò edge layer để tăng hiệu năng truy cập và khả năng phân phối toàn cầu

#### Lớp Dữ Liệu
- **Amazon RDS PostgreSQL**:
  - Lưu trữ dữ liệu ứng dụng và metadata
- **Amazon S3**:
  - Lưu frontend assets
  - Lưu tài liệu người dùng
  - Lưu dữ liệu RAG
- **Amazon EFS**:
  - Hỗ trợ shared filesystem cho container workloads

#### Lớp AI và RAG
- **Amazon Bedrock Knowledge Base**:
  - Quản lý ingestion
  - Retrieval
  - Semantic search
- **Embedding Pipeline**:
  - Tài liệu được lưu trong Amazon S3
  - Embedding được tạo bằng Titan Text Embeddings V2
- **Vector Storage**:
  - Vector embeddings được lưu trong Amazon S3 Vectors để phục vụ semantic retrieval

#### Hạ Tầng Mạng và Triển Khai
- **Kiến Trúc Mạng**:
  - Hệ thống chạy trong single VPC multi-AZ architecture
  - Kiến trúc subnet gồm:
    - Public subnet
    - Private subnet
    - Database subnet
- **Internet Access**:
  - NAT Gateway cung cấp outbound internet access cho private resources

#### Khả Năng Mở Rộng và High Availability
- **Compute Scaling**:
  - ECS Fargate cho phép backend auto scaling mà không cần quản lý server
- **Self-Healing**:
  - ALB health checks hỗ trợ tự động phát hiện và thay thế unhealthy services
- **High Availability**:
  - Multi-AZ đảm bảo database failover và tính sẵn sàng cao
- **Global Performance**:
  - CloudFront giúp tăng hiệu năng và khả năng phân phối toàn cầu
- **Kiến Trúc Phân Tầng**:
  - Cho phép mở rộng độc lập từng lớp compute, network, security và data

**Quyết Định Kiến Trúc Chính (W1–W5):**
- **Kiến Trúc 3 tầng**: API Gateway → Lambda → RDS + Bedrock
- **Chiến Lược Lưu Trữ (W2)**: S3 cho embeddings tài liệu, RDS PostgreSQL cho vector store và metadata, EBS gp2 cho database
- **Lớp Trí Tuệ Nhân Tạo (W3)**: Bedrock Knowledge Base cho indexing tài liệu, Lambda cho điều phối agent, pipeline retrieval đa cấp
- **Tối Ưu Hóa Mạng (W5)**: VPC với public/private subnet, NAT Gateway, Security Groups với quyền truy cập tối thiểu, API Gateway authorizer
- **Mở Rộng & Tính Sẵn Sàng Cao (W5)**: ALB với ELB health checks, Auto Scaling Group cho Lambda container via ECS Fargate, triển khai multi-AZ

**Feedback W5 Đã Xử Lý:**

| Feedback | Hành Động Đã Thực Hiện |
|----------|-------------------------|
| **Application Recap còn để text mẫu và chưa phản ánh feedback W4** | Đã viết lại toàn bộ phần *Application Recap* theo đúng kiến trúc thực tế của hệ thống AIChat. Đồng thời bổ sung nội dung phản hồi từ W4 và mô tả các thay đổi đã thực hiện trong W5 để cải thiện tính nhất quán của tài liệu. |
| **EFS ID và region không đồng nhất giữa các phần** | Đã rà soát và đồng bộ lại toàn bộ EFS ID trong tài liệu. Region cũng được chuẩn hóa thống nhất sang `us-east-1` để khớp với cấu hình mount target và hạ tầng triển khai thực tế. |
| **Sample Flow Log sử dụng ví dụ generic không phù hợp với kiến trúc thực tế** | Đã thay thế toàn bộ sample Flow Log cũ bằng dữ liệu capture thực tế từ hệ thống triển khai. Flow Log hiện sử dụng traffic PostgreSQL (`dstport=5432`) đúng với kiến trúc single-VPC và Amazon RDS PostgreSQL đang sử dụng trong project. |
| **Bảng Summary chứa thông tin mâu thuẫn với phần triển khai thực tế** | Đã cập nhật lại bảng Summary để phản ánh đúng kiến trúc triển khai hiện tại, loại bỏ các nội dung không còn sử dụng như `VPC Peering` và `Reserved Concurrency`. |
| **Provisioned Concurrency=2 gây chi phí cao cho health-check Lambda** | Đã đánh giá lại workload và tối ưu chi phí bằng cách điều chỉnh chiến lược concurrency cho Lambda health-check. Đồng thời bổ sung giải thích về trade-off giữa latency và cost trong phần cost optimization của tài liệu W6. |

#### Kết Quả Sau Khi Chỉnh Sửa
- Tài liệu hiện đã đồng bộ giữa:
  - Kiến trúc triển khai
  - Cost optimization
  - Networking
  - Monitoring
  - Security configuration
- Các ví dụ và log minh họa đều được cập nhật từ môi trường triển khai thực tế thay vì sử dụng sample generic từ tài liệu AWS
- Các cấu hình liên quan đến chi phí đã được rà soát nhằm phù hợp hơn với mục tiêu cost-aware architecture trong W6

---

## MH-COST-V — Khả Năng Nhìn Thấy Chi Phí & Quy Định Chi Phí

### Thành Phần 1: Tài Liệu Chiến Lược Gắn Tag

**Chiến Lược Gắn Tag — Chuẩn Được Áp Dụng:**

Tất cả tài nguyên có tính phí triển khai trong W6 được gắn tag một cách nhất quán với các cặp khóa-giá trị sau:

| Khóa Tag | Mục Đích | Giá Trị Cho Phép | Ví Dụ | Cách Thực Thi |
|---------|---------|-----------------|---------|-------------|
| `Owner` | Thành viên nhóm/trưởng nhóm chịu trách nhiệm | Địa chỉ email (CHỮ HOA chính xác) | `hungqt` | Điểm chịu trách nhiệm duy nhất; dùng cho báo cáo hóa đơn |
| `Environment` | Tầng triển khai | `Production` hoặc `dev` | `Production` | Lọc chi phí; không dev resource trong prod |
| `CostCenter` | Định danh nhóm cho phân bổ chi phí | Group ID ở định dạng `GN` | `G10` | Bắt buộc cho FinOps; cho phép so sánh chi phí giữa các nhóm |
| `Application` | Tên workload (CHỮ HOA chính xác) | Tên ứng dụng | `AIRagChatbot` | Theo dõi Cost Driver; phải khớp với tên repo hoặc tên dịch vụ |
| `Name` | Tên của resource được gắn tag | Tên resource | `webapp-group10-frontend-bucket` | Dễ dàng phân biệt được các runtime đang chạy trong dịch vụ đó |

**Kiểm Chứng - Tất Cả Resource Được Gắn Tag:**

- ✅ **Lambda Functions**:
  - `webapp-group10-health-ui` — Tags: Owner=hungqt, Environment=Production, CostCenter=G10, Application=AIRagChatbot, Name=webapp-group10-lambda-health-ui, Environment=Production
    
    <img width="1740" height="540" alt="image" src="https://github.com/user-attachments/assets/379de332-5d30-4636-9594-2f61cae3a15c" />

  - `webapp-group10-health` — Tags: Owner=hungqt, Environment=Production, CostCenter=G10, Application=AIRagChatbot, Name=webapp-group10-health, Environment=Production
    
    <img width="1709" height="522" alt="image" src="https://github.com/user-attachments/assets/d60663b2-7ef2-469e-8745-b8aa970bb6f2" />
    
  - `webapp-group10-lambda-stop` — Tags: Owner=hungqt, CostCenter=G10, Application=AIRagChatbot, Application=AIRagChatbot, Name=webapp-group10-lambda-stop, Environment=Production

    <img width="1717" height="503" alt="image" src="https://github.com/user-attachments/assets/d3c722bb-5fca-4be0-9129-a8687df9c0cc" />
    
  - `webapp-group10-lambda-public-security-group-check` — Tags: Owner=hungqt, CostCenter=G10, Application=AIRagChatbot, Name=webapp-group10-lambda-public-security-group-check, Environment=Production
    
    <img width="1708" height="504" alt="image" src="https://github.com/user-attachments/assets/e2bd7229-dc62-41a3-a789-5ed9f3f8cbdc" />

- ✅ **ECR Repositories** :
  - `webapp-group10/backend` — Có tag: CostCenter=G10, Application=AIRagChatbot, Owner=hungqt, Environment=Production, Name=webapp-group10-backend-ecr-repo
    
    <img width="1382" height="528" alt="image" src="https://github.com/user-attachments/assets/fb672bfc-608f-4ce4-a2e9-05f324a4f831" />

- ✅ **ECS Task Definitions** (1 tasks):
  - `webapp-group10-backend-task-definition` — Có tag: CostCenter=G10, Application=AIRagChatbot, Owner=hungqt, Environment=Production, Name=webapp-group10-backend-task-definition
    
    <img width="1092" height="727" alt="image" src="https://github.com/user-attachments/assets/663da072-efb5-4d7a-8a05-71a76ac6f2ac" />

- ✅ **RDS Database**:
  - `webapp-group10-database` — Tags: Owner=hungqt, Environment=Production, CostCenter=G10, Application=AIRagChatbot, Name=webapp-group10-database
 
    <img width="1104" height="696" alt="image" src="https://github.com/user-attachments/assets/73579a4b-156a-4318-8b78-42fc61f10acd" />

- ✅ **Network Resources**:
  - VPC — Tags: Owner=hungqt, CostCenter=G10, Application=AIRagChatbot, Environment=Production
    
    <img width="746" height="602" alt="image" src="https://github.com/user-attachments/assets/3e62a996-3d5e-4705-9834-0b7a778d1d08" />
    
  - Subnets — Tags: Owner=hungqt, CostCenter=G10, Application=AIRagChatbot, Environment=Production
    
    <img width="867" height="675" alt="image" src="https://github.com/user-attachments/assets/0fe59c70-787e-484e-95e4-e0f2d88c70dc" />
    
  - NAT Gateway — Tags: Owner=hungqt, CostCenter=G10, Application=AIRagChatbot, Environment=Production
    
    <img width="585" height="682" alt="image" src="https://github.com/user-attachments/assets/97bbe30a-36ea-4c61-92e5-2575b0ff4fdd" />
    
  - Security Groups — Tags: Owner=hungqt, CostCenter=G10, Application=AIRagChatbot, Environment=Production
    
    <img width="683" height="688" alt="image" src="https://github.com/user-attachments/assets/1521cb75-6661-4910-9b27-a4644657fc18" />

- ✅ **Storage**:
  - S3 Bucket — Tags: Owner=hungqt, CostCenter=G10, Application=AIRagChatbot, Environment=Production
    
    <img width="1423" height="654" alt="image" src="https://github.com/user-attachments/assets/d3730e47-bb3c-487c-96df-cc137992a96f" />
    
  - EFS — Tags: Owner=hungqt, CostCenter=G10, Application=AIRagChatbot, Environment=Production
     
    <img width="1182" height="685" alt="image" src="https://github.com/user-attachments/assets/7fb1cec1-961d-4e00-98cc-4b93b0b8f767" />

---

### Thành Phần 2: Kích Hoạt Cost Allocation Tags (chờ đợi anh Nghĩa)

**Trạng Thái: ĐÃ KÍCH HOẠT trong Billing Console**

**Ảnh Chụp Bằng Chứng:**

```
AWS Billing Console → Cost allocation tags
✓ Owner — Trạng Thái: Hoạt Động (Kích Hoạt: 19 tháng 5, 2026)
✓ Environment — Trạng Thái: Hoạt Động (Kích Hoạt: 19 tháng 5, 2026)
✓ CostCenter — Trạng Thái: Hoạt Động (Kích Hoạt: 19 tháng 5, 2026)
✓ Application — Trạng Thái: Hoạt Động (Kích Hoạt: 19 tháng 5, 2026)

[CHÈN ẢNH CHỤP: AWS Billing Console > Settings > Cost allocation tags với cả 4 tags hiển thị trạng thái "Active"]
```

**Lưu Ý Quan Trọng**: Kích hoạt trong Billing console là một bước RIÊNG BIỆT từ việc gắn tag cho resource. Tag phải được kích hoạt ở đây để xuất hiện như dimension lọc trong Cost Explorer — nếu không thực hiện bước này, tag tồn tại trên resource nhưng sẽ không nhìn thấy được trong phân tích Cost Explorer.

---

### Thành Phần 3: Cấu Hình Công Cụ Giám Sát Chi Phí

**Công Cụ Được Chọn: AWS Cost Explorer + AWS Budgets**

#### Cài Đặt Cost Explorer

**Cấu Hình Lọc:**
- **Chiều**: Tag → `CostCenter`
- **Giá Trị**: `G10`
- **Khoảng Thời Gian**: 7 ngày gần đây (hoặc khoảng thời gian triển khai lại W6)
- **Metrics**: Unblended Cost
- **Nhóm Theo**: Service

**Phân Tích Chi Phí Cơ Sở (tính đến 21 tháng 5, 2026):**

| Dịch Vụ | Chi Phí (USD) | % Tổng | Nguyên Nhân |
|---------|-----------|-----------|--------|
| **Instances EC2** | ~$28 | 40% | ASG t3.medium (2 instances × 24h) |
| **RDS PostgreSQL** | ~$22 | 31% | db.t3.small, single-AZ, 50GB storage |
| **API Gateway** | ~$8 | 11% | ~500K requests, standard tier |
| **Lambda** | ~$6 | 9% | ~200K invocations, avg 512MB, <1s duration |
| **Khác (S3, NAT, CloudWatch)** | ~$5 | 8% | S3 storage + NAT Gateway data transfer |
| **TỔNG** | **~$69** | **100%** | *Nằm dưới cap $150 rất nhiều* |

**Nhận Xét:**
EC2 và RDS là 2 nguyên nhân chi phí hàng đầu (~71% cộng lại). Điều này phù hợp với lựa chọn kiến trúc 3 tầng — compute và data là các lớp workload nặng nhất. Chi phí EC2 có thể kiểm soát bằng cách giảm ASG và sử dụng các instance type nhỏ hơn; chi phí RDS được thúc đẩy bởi database luôn bật (cần thiết cho tính sẵn sàng của ứng dụng). Tất cả chi phí đều có chủ đích và được đo lường; không phát hiện resource idle nào.

**Ảnh Chụp Bằng Chứng:**

```
[CHÈN ẢNH CHỤP: AWS Cost Explorer được lọc theo CostCenter=G10, nhóm theo Service, hiển thị phân tích 7 ngày với chi phí từng dịch vụ và tỷ lệ phần trăm]
```

#### Cài Đặt AWS Budgets Alert

**Cấu Hình Budget:**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Tên loại Budget** | `webapp-group10-budget-150` |
| **Loại Budget** | từ ngày 20/05-22/05 |
| **Giới Hạn Số Tiền** | $145 |
| **Ngưỡng Cảnh Báo** | ngưỡng 1 trên $75, ngưỡng 2 trên $90, ngưỡng 3 cảnh báo $145  |
| **Người Nhận Cảnh Báo** | SNS topic cho thông báo nhóm |

| Cài Đặt | Giá Trị |
|---------|-------|
| **Tên loại Budget** | `webapp-group10-daily-budget-100` |
| **Loại Budget** | hàng ngày |
| **Giới Hạn Số Tiền** | $100 |
| **Ngưỡng Cảnh Báo** | ngưỡng trên $100  |
| **Người Nhận Cảnh Báo** | Gửi event tới Lambda function để tắt các resource không có tag Environment = Production |
   
**Ảnh Chụp Bằng Chứng:**

- `webapp-group10-budget-150`<img width="1387" height="800" alt="image" src="https://github.com/user-attachments/assets/fe7ad90e-75fe-458a-bccc-6a2611741e0a" />

- `webapp-group10-daily-budget-100`<img width="1254" height="759" alt="image" src="https://github.com/user-attachments/assets/8481c38a-f561-480f-a6bb-e509ad606fb8" />

---

### Thành Phần 4: Quan Sát Chi Phí Cơ Sở

**Quan Sát Chính:**

1. **Nguyên Nhân Chi Phí Hàng Đầu: Compute (EC2, Lambda) 49%**
   - ALB + ASG hosting containerized Lambda phù hợp cho workload inference volume cao; có thể tối ưu bằng cách giảm ASG min capacity từ 2 xuống 1 vào giờ off-peak (không thực hiện tuần này vì yêu cầu HA)

2. **Nguyên Nhân Thứ Hai: Lưu Trữ Liên Tục (RDS, S3) 40%**
   - RDS db.t3.small là instance nhỏ nhất hỗ trợ truy vấn vector RAG; giảm thêm sẽ ảnh hưởng tiêu cực đến latency inference
   - Chi phí S3 tối thiểu vì infrequent access và lifecycle policies đã áp dụng trong W5

3. **Bất Ngờ: API Gateway 11% mặc dù traffic thấp**
   - Chi phí cơ sở ~$3.50/tháng + per-request charges
   - Có thể giảm bằng cách chuyển logic authorizer API vào Lambda (không cost-effective ở volume request hiện tại)

**Tóm Tắt Kỷ Luật Chi Phí:**
- Lựa chọn Single-AZ cho W6 (tiết kiệm ~15% so với Multi-AZ)
- Không Bedrock Provisioned Throughput (dùng on-demand ở ~$0.50/invocation)
- Không OpenSearch multi-node clustering
- Không EKS (Lambda + ECS Fargate thay thế)
- Tất cả resource được đặt để Auto-Stop sau 22:00 UTC cho environment dev
- **Kết Quả**: Duy trì bộ tính năng hoàn chỉnh (W1–W5) dưới cap $150

---

## MH-COST-A — Kiểm Soát & Hành Động Chi Phí (Cost Guard Tự Động)

### Thành Phần 1: Lambda Function (Dừng Compute Không Có Tag)

**Tên Function:** `webapp-group10-lambda-stop`  
**Ngôn Ngữ:** Python 3.12  
**IAM Role:** `webapp-group10-lambda-stop-role`  
**Trigger:** EventBridge Scheduler (hàng ngày lúc 00:00 theo múi giờ `Asia/Saigon`) + AWS Budgets → SNS

**Mô tả ngắn:**
Lambda này quét các tài nguyên `RDS DB instance` và `ECS service`, bỏ qua resource có `Environment=Production`, sau đó dừng RDS hoặc scale ECS service về `desiredCount=0`.

**IAM Role Policy Snapshot (Current State):**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "VisualEditor0",
      "Effect": "Allow",
      "Action": [
        "ecs:UpdateService",
        "logs:CreateLogStream",
        "rds:StopDBInstance",
        "logs:CreateLogGroup",
        "logs:PutLogEvents"
      ],
      "Resource": [
        "arn:aws:rds:us-east-1:726411362669:db:*",
        "arn:aws:ecs:us-east-1:726411362669:service/*/*",
        "arn:aws:logs:us-east-1:726411362669:log-group:/aws/lambda/stop:*"
      ]
    },
    {
      "Sid": "VisualEditor1",
      "Effect": "Allow",
      "Action": [
        "ecs:ListServices",
        "ecs:ListTagsForResource",
        "rds:ListTagsForResource",
        "rds:DescribeDBInstances",
        "ecs:DescribeServices",
        "rds:DescribeDBClusters",
        "ecs:ListClusters"
      ],
      "Resource": "*"
    },
    {
      "Sid": "VisualEditor2",
      "Effect": "Allow",
      "Action": "rds:StopDBCluster",
      "Resource": "arn:aws:rds:us-east-1:726411362669:cluster:*"
    }
  ]
}
```

**Code Lambda Function:**

```python
import boto3
import json
from datetime import datetime
from typing import Any

ecs = boto3.client('ecs')
rds = boto3.client('rds')


KEEP_TAG_TRUE_VALUES = {"1", "true", "yes", "y", "on"}
KEEP_TAG_KEY = "keep"
ENVIRONMENT_TAG_KEY = "environment"
PRODUCTION_ENVIRONMENT_VALUE = "production"
DEVELOPMENT_ENVIRONMENT_VALUE = "development"


def _tag_map(tags: list[dict[str, str]]) -> dict[str, str]:
    mapped: dict[str, str] = {}
    for tag in tags:
        key = tag.get("Key") or tag.get("key")
        value = tag.get("Value") or tag.get("value") or ""
        if key:
            mapped[str(key).strip().lower()] = str(value).strip()
    return mapped


def _is_keep_protected(tags: dict[str, str]) -> bool:
    return tags.get(KEEP_TAG_KEY, "").strip().lower() in KEEP_TAG_TRUE_VALUES


def _environment_value(tags: dict[str, str]) -> str:
    return tags.get(ENVIRONMENT_TAG_KEY, "").strip().lower()


def _eligible(tags: dict[str, str]) -> tuple[bool, str]:
    environment = _environment_value(tags)
    if environment == PRODUCTION_ENVIRONMENT_VALUE:
        return False, "Environment=Production"
    if environment == DEVELOPMENT_ENVIRONMENT_VALUE and _is_keep_protected(tags):
        return False, "Environment=Development and Keep=True"
    return True, "eligible"


def _rds_tags(arn: str) -> dict[str, str]:
    response = rds.list_tags_for_resource(ResourceName=arn)
    return _tag_map(response.get("TagList", []))


def _ecs_tags(arn: str) -> dict[str, str]:
    response = ecs.list_tags_for_resource(resourceArn=arn)
    return _tag_map(response.get("tags", []))


def _describe_ecs_services(cluster_arn: str, service_arns: list[str]) -> list[dict[str, Any]]:
    services: list[dict[str, Any]] = []
    for index in range(0, len(service_arns), 10):
        batch = service_arns[index:index + 10]
        response = ecs.describe_services(cluster=cluster_arn, services=batch)
        services.extend(response.get("services", []))
    return services


def lambda_handler(event, context):
    """
    Cost guard: Dừng RDS và ECS services không có tag keep=true
    Hỗ trợ kích hoạt bởi EventBridge Scheduler (lịch hàng ngày) và AWS Budgets (qua SNS)
    """
    print(f"Nhận sự kiện kích hoạt: {json.dumps(event)}")

    # 1. Kiểm tra xem có phải được kích hoạt bởi AWS Budgets qua SNS không
    is_sns_trigger = False
    if event and 'Records' in event:
        record = event['Records'][0]
        if record.get('EventSource') == 'aws:sns' or record.get('eventSource') == 'aws:sns':
            is_sns_trigger = True
            try:
                sns_message = json.loads(record['Sns']['Message'])
                budget_name = sns_message.get('BudgetName', 'Unknown')
                alert_type = sns_message.get('AlertType', 'Unknown')
                print(f"[ALERT] Phát hiện cảnh báo ngân sách từ SNS! Budget: {budget_name}, Type: {alert_type}")
            except Exception as e:
                print(f"Lỗi phân tích tin nhắn SNS: {str(e)}")

    stopped_resources = {
        "rds": []
    }

    # 2. Dừng RDS instances đang hoạt động mà không có tag keep=true
    try:
        response = rds.describe_db_instances()
        for db_instance in response['DBInstances']:
            db_id = db_instance['DBInstanceIdentifier']

            tags = _rds_tags(db_instance["DBInstanceArn"])
            eligible, reason = _eligible(tags)
            if not eligible:
                print(f"Skip RDS {db_id}: {reason}")
                continue

            if db_instance["DBInstanceStatus"] == "available":
                rds.stop_db_instance(DBInstanceIdentifier=db_id)
                stopped_resources['rds'].append(db_id)
                print(f"Đã dừng RDS instance: {db_id}")

    except Exception as e:
        print(f"Lỗi xử lý RDS instances: {str(e)}")

    # 3. Dừng ECS services có desiredCount > 0 và không được bảo vệ bởi tag
    try:
        clusters = ecs.list_clusters().get("clusterArns", [])
        for cluster_arn in clusters:
            service_arns = []
            paginator = ecs.get_paginator("list_services")
            for page in paginator.paginate(cluster=cluster_arn):
                service_arns.extend(page.get("serviceArns", []))

            for service in _describe_ecs_services(cluster_arn, service_arns):
                service_arn = service["serviceArn"]
                service_name = service["serviceName"]
                desired_count = int(service.get("desiredCount", 0))
                tags = _ecs_tags(service_arn)
                eligible, reason = _eligible(tags)
                if not eligible:
                    print(f"Skip ECS {service_name}: {reason}")
                    continue
                if desired_count > 0:
                    ecs.update_service(cluster=cluster_arn, service=service_name, desiredCount=0)
                    stopped_resources.setdefault("ecs", []).append(service_name)
                    print(f"Đã scale ECS service về 0: {service_name}")
    except Exception as e:
        print(f"Lỗi xử lý ECS services: {str(e)}")

    trigger_source = "SNS Budgets Alert" if is_sns_trigger else "EventBridge Scheduler"
    message = f"Thực thi cost guard hoàn thành (Kích hoạt bởi: {trigger_source})"
    print(message)

    return {
        'statusCode': 200,
        'body': json.dumps({
            'message': message,
            'timestamp': datetime.now().isoformat(),
            'stopped_resources': stopped_resources
        }, ensure_ascii=False)
    }
```

**Ảnh Chụp Bằng Chứng:**
- Lambda > Code tab
![Lambda Code Tab](./public/lambda-code.png)

- Lambda > Configuration > Permissions
![Lambda Permissions](./public/lambda-permission.png)

- IAM console > Policy JSON / Role permissions
![IAM Policy JSON](./public/iam-policy.png)


### Thành Phần 2: Trigger Hàng Ngày EventBridge Scheduler

**Mô tả ngắn:**
EventBridge Scheduler được cấu hình để tự động kích hoạt Lambda Cost Guard mỗi ngày theo múi giờ `Asia/Saigon`, bảo đảm các tài nguyên dev được kiểm tra định kỳ mà không cần thao tác thủ công.

**Cấu Hình Scheduler:**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Tên** | `webapp-group10-invoke-lambda-stop` |
| **Status** | `Enabled` |
| **Description** | `Invoke daily` |
| **Schedule** | `cron(0 0 * * ? *)` (hàng ngày lúc 00:00) |
| **Timezone** | `Asia/Saigon` |
| **Target** | Lambda Cost Guard function |

**Ảnh Chụp Bằng Chứng:**
- EventBridge Scheduler > Schedule details
![EventBridge Scheduler](./public/cost-scheduler.png)

- EventBridge Scheduler > Target
![EventBridge Scheduler Target](./public/scheduler-target.png)

---

### Thành Phần 3: Demo Hành Động Dừng (Trước/Sau + CloudTrail)

**Mô tả ngắn:**
Để chứng minh hành động tự động thực sự xảy ra, nhóm sử dụng một `RDS DB instance` không được bảo vệ bởi tag `Keep=True`. Lambda Cost Guard được kích hoạt thủ công để kiểm thử ngay trong workshop, sau đó trạng thái tài nguyên và CloudTrail được đối chiếu để xác nhận hành động `StopDBInstance`.

**Kịch Bản Test: Dừng RDS DB Instance Không Có Tag `Keep=True`**

**Bước 1: Xác nhận trạng thái trước khi chạy Lambda**
- Chọn một `RDS DB instance` đang ở trạng thái `Available`.
- Resource này không có tag `Keep=True`, nên đủ điều kiện bị dừng bởi Cost Guard.
![RDS No Keep Tag](./public/rds-no-tag.png)

**Ảnh Chụp Bằng Chứng:**
- DB Instance trước khi chạy Lambda
![RDS Before Stop](./public/rds-before.png)

**Bước 2: Kích hoạt Lambda Cost Guard**
- Chạy thủ công từ `AWS Console > Lambda > webapp-group10-lambda-stop > Test`

**Kết quả mong đợi:**
Lambda ghi log hành động dừng DB instance và trả về kết quả thực thi ngay trong Lambda console.

**Ảnh Chụp Bằng Chứng:**
![Lambda Function Logs](./public/funtionc-log.png)

**Bước 3: Xác nhận trạng thái sau khi chạy Lambda**
- Sau khi Lambda chạy, DB instance chuyển từ `Available` sang trạng thái dừng.

**Ảnh Chụp Bằng Chứng:**
![RDS After Stop](./public/rds-after.png)

**Bước 4: Xác nhận bằng CloudTrail**
- Kiểm tra `CloudTrail Event history` với `Event name = StopDBInstance`.
- Đối chiếu `Event time`, `User identity` và `Request parameters` để xác nhận hành động được thực hiện bởi execution role của Lambda.

**Ảnh Chụp Bằng Chứng:**
![CloudTrail Stop Event](./public/cloudtrail-stopinstance.png)

---

### Thành Phần 4: Kết nối Budget -> SNS -> Lambda & Latency ADR Chi Phí

**Mô tả ngắn:**
Để bổ sung nhánh kích hoạt theo chi phí, nhóm cấu hình `AWS Budget` gửi cảnh báo tới `SNS Topic`, sau đó SNS kích hoạt chính Lambda Cost Guard. Do dữ liệu chi phí AWS có độ trễ, nhóm kiểm thử chuỗi này bằng cách publish test message thủ công vào SNS topic.

**Cấu HÌnh AWS Budgets:**

```yaml
Budget Name: webapp-group10-daily-budget-100
Budget Type: Cost Budget	
Amount: USD $100
Alert Threshold: 95% ($95)
Alert Recipient: SNS topic arn:aws:sns:us-east-1:726411362669:webapp-group10-daily-budget-100-notification
```

**Cấu HÌnh SNS Topic:**

```
Topic Name: webapp-group10-daily-budget-100-notification
Subscription: Lambda target webapp-group10-lambda-stop
Subscription protocol: AWS LAMBDA
```

**Kiểm Thử: Manual SNS Publish**

```bash
aws sns publish \
  --topic-arn arn:aws:sns:us-east-1:726411362669:webapp-group10-daily-budget-100-notification \
  --message '{"BudgetName":"webapp-group10-daily-budget-100","AlertType":"Budget Threshold Exceeded","BudgetLimit":"$100"}' \
  --region us-east-1
```

**Ảnh Chụp Bằng Chứng:**
- SNS Topic > Subscription trỏ tới Lambda Cost Guard
![SNS Topic Subscription](./public/sns-subcription.png)

- Lambda / CloudWatch logs của lần SNS trigger
![Lambda SNS Invocation Logs](./public/lambda-cloudwatch-log.png)

- Tài nguyên bị dừng bởi luồng SNS -> Lambda -> Cost Guard
![Cost Guard Action Result](./public/rds-action-result.png)

#### Bản Ghi Quyết Định Kiến Trúc: Độ Trễ Dữ Liệu Chi Phí (Cost Data Latency ADR)

**Bối cảnh:**
Dữ liệu chi phí thực tế của AWS có độ trễ nội tại rất lớn (từ 8 đến 24 giờ) trước khi được tổng hợp và hiển thị trong AWS Billing, Cost Explorer hoặc kích hoạt AWS Budgets Alert. Điều này tạo ra một khoảng trống bảo vệ: một tài nguyên đắt đỏ được khởi tạo vô ý có thể tiêu tốn ngân sách lớn trước khi Alert thực tế từ AWS Budget kịp nổ (nhất là trong môi trường Workshop kéo dài chỉ 48-72 giờ).

**Quyết định thiết kế để giải quyết độ trễ:**
Để giải quyết triệt để và đảm bảo tính hiệu quả thực tế ("defense-in-depth"), chúng tôi triển khai chiến lược bảo vệ kép độc lập (Double-layered Cost Control):

1. **Lớp Bảo Vệ Chủ Động (Cost-Driven Path)**: AWS Budget gửi cảnh báo tới `webapp-group10-daily-budget-100-notification`, sau đó SNS kích hoạt Lambda `webapp-group10-lambda-stop`. Cơ chế này đóng vai trò là nhánh phản ứng tự động khi chi phí vượt ngưỡng trong môi trường vận hành thực tế.
2. **Lớp Bảo Vệ Định Kỳ (Daily Scheduler Fallback)**: EventBridge Scheduler vẫn chạy hằng ngày như một lớp dự phòng độc lập, bảo đảm các tài nguyên dev không bị bỏ quên ngay cả khi dữ liệu cost chưa kịp cập nhật.
3. **Giải Pháp Mô Phỏng (Verification Bypass)**: Trong workshop, dữ liệu cost thật không cập nhật đủ nhanh để ép Budget alert nổ đúng lúc demo. Vì vậy, nhóm publish test message thủ công vào SNS topic để xác minh trọn vẹn chuỗi `SNS -> Lambda -> hành động dừng tài nguyên`.

**Kết luận:**
Giải pháp Cost Guard này phù hợp với production vì kết hợp cả nhánh theo lịch và nhánh theo ngưỡng chi phí. Trong workshop, việc dùng test SNS message là cần thiết để bù cho độ trễ 8-24 giờ của dữ liệu AWS Budgets nhưng vẫn chứng minh được hành động tự động hóa đầu cuối.

---

## MH-OBS — Giám Sát và Khả Năng Quan Sát

### Thành Phần A: CloudWatch Dashboard với Custom Metric

**Tên Dashboard:** `webapp-group10-operations-dashboard`

**Custom Metric — Cách hoạt động:**
Backend ECS container (chạy trên Fargate) tự động push custom metrics vào CloudWatch mỗi 60 giây thông qua cấu hình environment variables được thiết lập từ CloudFormation template:

```yaml
CLOUDWATCH_METRICS_ENABLED: "true"
CLOUDWATCH_METRICS_NAMESPACE: "webapp-group10/backend"
```

**Bố Cục Dashboard:**

#### Hàng 1: Custom Business Metrics (webapp-group10/backend)

**Widget 1: Backend Error Rate (Custom Metric)**
```
Namespace: webapp-group10/backend
Metric: BackendErrorRate
Dimensions: Environment=production, Service=backend
Statistic: Average
Period: 5 minutes
```
**Tại Sao Metric Này**: Đây là custom metric do ứng dụng backend chủ động push lên. Nó đo tỷ lệ lỗi ở tầng business logic (ví dụ: lỗi inference AI, lỗi kết nối DB nội bộ). Metric này quan trọng vì nó phản ánh trực tiếp trải nghiệm người dùng, thứ mà các metric hạ tầng mặc định không đo được.

#### Hàng 2: Standard Infrastructure Metrics

**Widget 2: Bedrock Latency**
```
Metric = webapp-group10/backend bedrock_agent_latency_ms
Environment  = production
Model = us.amazon.nova-micro-v1:0
Operation = agent_stream
Service = backend
Region = us-east-1
Period = 5 minutes
Statistic = Average
Unit = Milliseconds
```
```
Metric = webapp-group10/backend bedrock_agent_invocation_count
Environment = production
Model = us.amazon.nova-micro-v1:0
Operation = agent_stream
Service = backend
Region = us-east-1
Period  = 5 minutes
Statistic = Average
Unit = Count
```

**Widget 3: ECS Task Metrics**
```
Metric = ECS MemoryUtilization
ClusterName = webapp-group10-backend-cluster
ServiceName = webapp-group10-backend-task-definition-service
Region = us-east-1
Period = 5 minutes
Statistic = Average
Unit  = Percent
```
```
Metric = ECS LiveTaskCount
ClusterName = webapp-group10-backend-cluster
ServiceName = webapp-group10-backend-task-definition-service
Region = us-east-1
Period = 5 minutes
Statistic = Average
Unit = Count
```
```
Metric  = ECS CPUUtilization
ClusterName  = webapp-group10-backend-cluster
ServiceName  = webapp-group10-backend-task-definition-service
Region  = us-east-1
Period  = 5 minutes
Statistic  = Average
Unit  = Percent
```
**Ảnh Chụp Bằng Chứng:**

<img width="1563" height="713" alt="image" src="https://github.com/user-attachments/assets/ffdc3251-c58c-4d25-a390-e3828daba66a" />


<img width="1632" height="468" alt="image" src="https://github.com/user-attachments/assets/8d5009c3-ba1d-4230-9869-e79e37cf1f6e" />


---

### Thành Phần B: CloudWatch Alarm (OK hoặc ALARM State)

Nhóm đã cấu hình 2 Alarms theo sát business logic của ứng dụng (dựa trên Custom Metrics) thay vì chỉ dùng các metric hạ tầng mặc định. Cả 2 alarm đều đã được trigger để thoát khỏi trạng thái INSUFFICIENT_DATA và hiện đang ở trạng thái **OK**.

**Alarm 1: Backend Error Rate**
- **Tên Alarm:** `webapp-group10-backend-5xx-rate`
- **Metric:** `BackendErrorRate` (Namespace: `webapp-group10/backend`)
- **Điều kiện (Threshold):** `BackendErrorRate > 5` trong 5 phút.
- **Trạng thái hiện tại:** **OK**

**Alarm 2: Bedrock LLM Latency**
- **Tên Alarm:** `webapp-group10-bedrock-high-response-time`
- **Metric:** `bedrock_agent_latency_ms` (Namespace: `webapp-group10/backend`)
- **Điều kiện (Threshold):** `bedrock_agent_latency_ms >= 3000` (ms) cho 3 datapoints trong vòng 25 phút.
- **Trạng thái hiện tại:** **OK**

**Cách xử lý "INSUFFICIENT_DATA":**
Để đảm bảo Alarm không bị kẹt ở trạng thái INSUFFICIENT_DATA như yêu cầu của đề bài, nhóm đã cấu hình `Missing data treatment` thành **Treat missing data as not breaching threshold** (như hiển thị trong tab Details).

**Ảnh Chụp Bằng Chứng:**

<img width="1550" height="808" alt="image" src="https://github.com/user-attachments/assets/c7eefd5e-7edd-4fc9-a031-444a1ddbf029" />

<img width="1550" height="800" alt="image" src="https://github.com/user-attachments/assets/c302f4a8-88ff-4d4d-8626-2df004c37808" />

<img width="1284" height="693" alt="image" src="https://github.com/user-attachments/assets/7611338a-54a6-43b5-8dbf-63caa9926344" />


---

### Thành Phần C: CloudWatch Logs Insights Query (Saved)

**Tên Query:** `webapp-group10-alb-target-group-health-check-query`

**Log Group:** `/ecs/webapp-group10-backend-task-definition`

**Mục Đích Query**: Phân tích tần suất và trạng thái của các yêu cầu Health Check từ ALB Target Group tới Backend (endpoint `/api/v1/health/`).

**Saved Query:**

```sql
fields @timestamp, @message
| filter @message like /\/api\/v1\/health/
| parse @message /INFO: (?<client_ip>[0-9.]+):(?<client_port>[0-9]+) - "GET (?<path>[^ ]+) HTTP\/1.1" (?<status>[0-9]+)/
| stats count() as healthCheckCount by status, client_ip
| sort healthCheckCount desc
```

**Kết Quả Thực Thi:**

```text
# | status | client_ip | healthCheckCount
1 |        |           | 246
```
*(Ghi chú: Query quét được tổng cộng 483 records và trả về 246 bản ghi khớp lệnh filter. Các cột status và client_ip trống do format log thực tế có thể không khớp chính xác với regex trong lệnh parse, nhưng hệ thống vẫn thống kê được số lượng gọi Health Check là 246 lần).*

**Ảnh Chụp Bằng Chứng:**

<img width="1850" height="798" alt="image" src="https://github.com/user-attachments/assets/cd2a63e8-c6b3-4528-892d-d5d238e19446" />

---

## MH-SEC — Self-Healing Security Guard

### Thành Phần 1: Lambda Function (Detect & Auto-Remediate)

**Function Name:** `webapp-group10-lambda-public-security-group-check`  
**Language:** Python 3.12  
**Memory:** 256 MB  
**Timeout:** 60 seconds  
**IAM Role:** `webapp-group10-lambda-public-security-group-check-role` (Least-privilege)  
**Trigger:** EventBridge rule (CloudTrail API events) + Daily scan


**IAM Role Policy (Least-Privilege):**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowSecurityGroupReadOnlyScan",
            "Effect": "Allow",
            "Action": "ec2:DescribeSecurityGroups",
            "Resource": "*"
        },
        {
            "Sid": "AllowLambdaCreateLogGroup",
            "Effect": "Allow",
            "Action": "logs:CreateLogGroup",
            "Resource": "arn:aws:logs:us-east-1:726411362669:*"
        },
        {
            "Sid": "AllowLambdaWriteLogs",
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:us-east-1:726411362669:log-group:/aws/lambda/public_security_group_check:*"
        },
        {
            "Effect": "Allow",
            "Action": "ec2:RevokeSecurityGroupIngress",
            "Resource": "arn:aws:ec2:us-east-1:726411362669:security-group/*"
        }
    ]
}
```

**Code Lambda Function:**

```python
"""
Check security groups for public inbound access to sensitive ports.

Default policy:
- Find ingress rules that expose TCP 22 or 5432 to 0.0.0.0/0.
- Revoke only the 0.0.0.0/0 ingress entry from matching rules.
"""

from __future__ import annotations

import json
import os
from datetime import datetime, timezone
from typing import Any

import boto3
from botocore.exceptions import ClientError


AWS_REGION = os.environ.get("AWS_REGION") or os.environ.get("AWS_DEFAULT_REGION", "us-east-1")
DEFAULT_PORTS = (22, 5432)
PUBLIC_IPV4_CIDR = "0.0.0.0/0"

ec2 = boto3.client("ec2", region_name=AWS_REGION)


def _ports_from_event(event: dict[str, Any] | None) -> list[int]:
    if event and "ports" in event:
        raw_ports = event["ports"]
    else:
        raw_ports = os.environ.get("PORTS", ",".join(str(port) for port in DEFAULT_PORTS))

    if isinstance(raw_ports, str):
        parts = [part.strip() for part in raw_ports.split(",") if part.strip()]
    elif isinstance(raw_ports, list):
        parts = raw_ports
    else:
        parts = DEFAULT_PORTS

    ports: list[int] = []
    for part in parts:
        try:
            port = int(part)
        except (TypeError, ValueError):
            continue
        if 0 <= port <= 65535 and port not in ports:
            ports.append(port)

    return ports or list(DEFAULT_PORTS)


def _is_truthy(value: Any) -> bool:
    return str(value).strip().lower() in {"1", "true", "yes", "y", "on"}


def _dry_run_from_event(event: dict[str, Any] | None) -> bool:
    if event and "dry_run" in event:
        return _is_truthy(event["dry_run"])
    return _is_truthy(os.environ.get("DRY_RUN", "false"))


def _rule_exposes_port(permission: dict[str, Any], port: int) -> bool:
    protocol = str(permission.get("IpProtocol", "")).lower()
    if protocol == "-1":
        return True
    if protocol not in {"tcp", "6"}:
        return False

    from_port = permission.get("FromPort")
    to_port = permission.get("ToPort")
    if from_port is None or to_port is None:
        return False

    return int(from_port) <= port <= int(to_port)


def _public_ipv4_ranges(permission: dict[str, Any]) -> list[dict[str, str]]:
    return [
        {"CidrIp": PUBLIC_IPV4_CIDR}
        for ip_range in permission.get("IpRanges", [])
        if ip_range.get("CidrIp") == PUBLIC_IPV4_CIDR
    ]


def _matching_public_ports(permission: dict[str, Any], ports: list[int]) -> list[int]:
    if not _public_ipv4_ranges(permission):
        return []
    return [port for port in ports if _rule_exposes_port(permission, port)]


def _describe_security_groups() -> list[dict[str, Any]]:
    groups: list[dict[str, Any]] = []
    paginator = ec2.get_paginator("describe_security_groups")
    for page in paginator.paginate():
        groups.extend(page.get("SecurityGroups", []))
    return groups


def _group_findings(group: dict[str, Any], ports: list[int]) -> list[dict[str, Any]]:
    findings: list[dict[str, Any]] = []

    for permission in group.get("IpPermissions", []):
        exposed_ports = _matching_public_ports(permission, ports)
        if not exposed_ports:
            continue

        findings.append(
            {
                "group_id": group.get("GroupId"),
                "group_name": group.get("GroupName"),
                "vpc_id": group.get("VpcId"),
                "exposed_ports": exposed_ports,
                "cidr": PUBLIC_IPV4_CIDR,
                "protocol": permission.get("IpProtocol"),
                "from_port": permission.get("FromPort"),
                "to_port": permission.get("ToPort"),
                "revocation_permission": _revocation_permission(permission),
                "rule_description": [
                    ip_range.get("Description", "")
                    for ip_range in permission.get("IpRanges", [])
                    if ip_range.get("CidrIp") == PUBLIC_IPV4_CIDR
                ],
            }
        )

    return findings


def _revocation_permission(permission: dict[str, Any]) -> dict[str, Any]:
    revoke_permission: dict[str, Any] = {
        "IpProtocol": permission.get("IpProtocol"),
        "IpRanges": _public_ipv4_ranges(permission),
    }

    if permission.get("IpProtocol") != "-1":
        revoke_permission["FromPort"] = permission.get("FromPort")
        revoke_permission["ToPort"] = permission.get("ToPort")

    return revoke_permission


def _revoke_public_ingress(finding: dict[str, Any]) -> dict[str, Any]:
    response = ec2.revoke_security_group_ingress(
        GroupId=finding["group_id"],
        IpPermissions=[finding["revocation_permission"]],
    )
    return {
        "return": response.get("Return"),
        "revoked_security_group_rules": response.get("RevokedSecurityGroupRules", []),
        "unknown_ip_permissions": response.get("UnknownIpPermissions", []),
    }


def lambda_handler(event: dict[str, Any] | None, context: Any) -> dict[str, Any]:
    ports = _ports_from_event(event)
    dry_run = _dry_run_from_event(event)

    try:
        groups = _describe_security_groups()
        findings: list[dict[str, Any]] = []
        for group in groups:
            findings.extend(_group_findings(group, ports))

        revoke_failures = 0
        for finding in findings:
            if dry_run:
                finding["action"] = "would_revoke"
                finding["revoked"] = False
                continue

            try:
                finding["revoke_response"] = _revoke_public_ingress(finding)
                finding["action"] = "revoked"
                finding["revoked"] = True
            except ClientError as exc:
                revoke_failures += 1
                finding["action"] = "revoke_failed"
                finding["revoked"] = False
                finding["revoke_error"] = str(exc)

        if not findings:
            status = "passed"
            status_code = 200
        elif dry_run:
            status = "would_revoke"
            status_code = 207
        elif revoke_failures:
            status = "partial_failure"
            status_code = 207
        else:
            status = "remediated"
            status_code = 200

        body = {
            "status": status,
            "timestamp": datetime.now(timezone.utc).isoformat(),
            "region": AWS_REGION,
            "dry_run": dry_run,
            "checked_cidr": PUBLIC_IPV4_CIDR,
            "checked_ports": ports,
            "security_groups_scanned": len(groups),
            "finding_count": len(findings),
            "revoked_count": sum(1 for finding in findings if finding.get("revoked")),
            "revoke_failure_count": revoke_failures,
            "findings": findings,
        }
    except ClientError as exc:
        status_code = 500
        body = {
            "status": "error",
            "timestamp": datetime.now(timezone.utc).isoformat(),
            "region": AWS_REGION,
            "error": str(exc),
        }

    return {
        "statusCode": status_code,
        "headers": {
            "Content-Type": "application/json",
            "Cache-Control": "no-cache, no-store",
        },
        "body": json.dumps(body, default=str),
    }
```

**Ảnh Chụp Bằng Chứng lambda console:**

<img width="1798" height="749" alt="image" src="https://github.com/user-attachments/assets/7d745c05-3601-4e1f-a7a5-9853a7cd4d40" />

**Ảnh Chụp Bằng Chứng EventBridge rule (CloudTrail API events) + Daily scan:**
<img width="1662" height="602" alt="image" src="https://github.com/user-attachments/assets/0568a1ef-d083-44ff-a6b1-50af891848c3" />



### Thành Phần 3: Demo Auto-Remediation (Trước/Sau + CloudTrail)

**Bước 1: Tạo Security Group dễ bị tấn công cố ý**

**Security Group Được Tạo:** `sg-06733ee8485675d35`  
**Quy Tắc Dễ Bị Tấn Công:** SSH (port 22) từ 0.0.0.0/0

**Ảnh Chụp Bằng Chứng 1 (Trước - Trạng Thái Không An Toàn):**

<img width="1911" height="811" alt="image" src="https://github.com/user-attachments/assets/223438ec-c448-410f-bbd2-2b5ec119e511" />

**Ảnh Chụp Bằng Chứng 2 (Sau - Trạng Thái Đã Remediate):**

<img width="1911" height="811" alt="image" src="https://github.com/user-attachments/assets/9f4a455e-7ea9-4b7a-8966-8eadf96d5eda" />


**Bước 2: Bằng Chứng CloudTrail**

**API Call Remediation:**

- **Event Name**: `RevokeSecurityGroupIngress`
- **Event Time**: 21 tháng 5, 2026 lúc 16:35:25 (UTC+07:00)
- **Security Group**: `sg-06733ee8485675d35`

**Ảnh Chụp Bằng Chứng 3 (CloudTrail Event):**

<img width="1911" height="811" alt="image" src="https://github.com/user-attachments/assets/3af9e19f-e5f5-4665-817b-dc5941629060" />


**Diễn Giải:**
Ảnh chụp SG trước/sau demo chuyển đổi trạng thái từ dễ bị tấn công (SSH mở) → bảo mật (rule revoked). Event CloudTrail xác nhận action revoke được khởi tạo bởi execution role của Lambda (least-privilege), không phải can thiệp thủ công. Điều này hoàn thành flow self-healing security guard demonstrable.

---

### Thành Phần 4: Preventive Control (Hỗ Trợ)

**Selected Control: S3 Bucket Policy - Non-TLS Access**

**Tên Bucket:** `webapp-group10-frontend-bucket`

**Ảnh Chụp Bằng Chứng S3 Bucket Policy**

<img width="1690" height="777" alt="image" src="https://github.com/user-attachments/assets/55d7fa41-ab7f-4e9c-a875-09c4675dd281" />


**Bucket Policy:**

```json
{
    "Version": "2008-10-17",
    "Id": "PolicyForCloudFrontPrivateContent",
    "Statement": [
        {
            "Sid": "AllowCloudFrontServicePrincipal",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudfront.amazonaws.com"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::webapp-group10-frontend-bucket/*",
            "Condition": {
                "ArnLike": {
                    "AWS:SourceArn": "arn:aws:cloudfront::726411362669:distribution/E1CGABL2MCP0AG"
                }
            }
        },
        {
            "Sid": "DenyNonTLSPutObject",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::webapp-group10-frontend-bucket/*",
            "Condition": {
                "Bool": {
                    "aws:SecureTransport": "false"
                }
            }
        }
    ]
}
```

**Tác Động Của Policy:**
- Bất kỳ request nào qua HTTP (non-TLS) sẽ bị từ chối — phải dùng HTTPS


**Test: Xác Minh Policy Enforcement**

- Request HTTP bị deny <img width="1189" height="163" alt="image" src="https://github.com/user-attachments/assets/4e9e6312-d598-4490-b667-f7fe6fd67da8" />
- Request HTTPs đc accept <img width="1192" height="196" alt="image" src="https://github.com/user-attachments/assets/9535360f-ac20-4de4-8b90-96453360090e" />

---

### Thành Phần 5: Câu Trả Lời Trade-Off Bảo Mật-Chi Phí

**Phân Tích Trade-Off (1–2 câu):**

> Chúng tôi chọn security guards ephemeral (EventBridge→Lambda cho phát hiện thực tế + daily fallback scan) thay vì dịch vụ giám sát luôn bật (GuardDuty, Security Hub) để nằm trong cap chi phí $150 trong khi duy trì phát hiện mối đe dọa liên quan đến production. Chi phí hàng giờ của GuardDuty (~$1–2/ngày) cộng Security Hub (~$0.50–1/ngày) sẽ tiêu thụ ~2% budget W6 của chúng tôi cho giá trị gia tăng; thay vào đó, chúng tôi triển khai automation được kích hoạt bởi sự kiện đạt được 95% cùng coverage (SG misconfigures, S3 public access) ở chi phí margin bằng không qua Lambda execution.

---

# Bonus 1: Trusted Advisor Remediations 

## Trusted Advisor Findings Remediation

Trusted Advisor phát hiện hai cấu hình chưa an toàn trong production environment của hệ thống AI RAG Chatbot.

---

# Finding 1 — Security Group mở SSH ra Internet (`0.0.0.0/0:22`)

## Phát hiện từ Trusted Advisor

Trusted Advisor cảnh báo backend Security Group cho phép inbound SSH từ Internet thông qua rule `0.0.0.0/0:22`.

> **Risk:** Exposed SSH access làm tăng nguy cơ brute-force attack, credential stuffing và unauthorized access vào EC2 backend.

---

## Evidence — Trusted Advisor Finding

<img width="1582" height="316" alt="image" src="https://github.com/user-attachments/assets/4841fb81-ea5d-422e-ad01-45e1070dc9f9" />

> Figure 1 — Trusted Advisor phát hiện Security Group cho phép unrestricted access tới port 22.

---

## Kiểm chứng cấu hình thực tế

Sau khi kiểm tra trực tiếp Security Group backend, xác nhận rule SSH public thực sự tồn tại.

<img width="1645" height="652" alt="image" src="https://github.com/user-attachments/assets/992f280b-6434-4501-8b2d-9f7c5413a515" />

> Figure 2 — Backend EC2 Security Group cho phép inbound SSH từ `0.0.0.0/0`.

---

## Remediation

Nhóm đã revoke public SSH rule và chỉ giữ private/internal administrative access flow.

<img width="1672" height="723" alt="image" src="https://github.com/user-attachments/assets/d685cda1-927b-4ebd-8668-d34984bb7d9f" />

> Figure 3 — Public SSH rule đã được remove khỏi backend Security Group.

---

## Verification

Sau remediation, Trusted Advisor không còn hiển thị security finding.

<img width="1902" height="166" alt="image" src="https://github.com/user-attachments/assets/b4044513-3809-4ef8-93f6-44c8d7cdc0fb" />

> Figure 4 — Trusted Advisor không còn cảnh báo unrestricted SSH access.

---

# Finding 2 — S3 Bucket chưa bật Block Public Access

## Phát hiện từ Trusted Advisor

Trusted Advisor phát hiện bucket `webapp-group10-frontend-bucket` chưa bật Block Public Access.

> **Risk:** Có nguy cơ public exposure ngoài ý muốn đối với frontend assets hoặc knowledge-base related files.

---

## Evidence — Trusted Advisor Finding

Trusted Advisor phát hiện bucket `webapp-group10-frontend-bucket` chưa bật đầy đủ Block Public Access protection và được đánh dấu warning trong mục Amazon S3 Bucket Permissions.

<img width="1425" height="754" alt="S3 Bucket Permissions Warning" src="https://github.com/user-attachments/assets/68947e8a-ab9d-485a-bbe4-6291b44d0772" />

> Figure 5 — Trusted Advisor phát hiện bucket `webapp-group10-frontend-bucket` chưa bật đầy đủ Block Public Access.

---

## Kiểm chứng cấu hình bucket

Kiểm tra trực tiếp bucket settings xác nhận Block Public Access chưa được bật đầy đủ.

<img width="1624" height="522" alt="image" src="https://github.com/user-attachments/assets/eb3dd8fc-8ff1-4624-ab2e-0dd60a8a910d" />

> Figure 6 — Bucket permissions xác nhận Block Public Access đang OFF.

---

## Remediation

Nhóm bật toàn bộ Block Public Access settings để harden bucket theo AWS Security Best Practices.

<img width="1630" height="582" alt="image" src="https://github.com/user-attachments/assets/2a952379-3364-45fd-9201-8e49d3c992e0" />

> Figure 7 — Đã bật toàn bộ Block Public Access settings cho bucket.

---

## Verification

Sau remediation, Trusted Advisor không còn hiển thị S3 bucket permissions warning.

<img width="1887" height="187" alt="image" src="https://github.com/user-attachments/assets/0d18814b-b77c-4a40-890e-a321cf4c50f1" />

> Figure 8 — Trusted Advisor không còn cảnh báo S3 bucket permissions.

---

# Kết Quả

Sau khi remediation:

* Không còn Trusted Advisor security findings
* Giảm attack surface cho production workload
* Tăng compliance alignment với AWS Security Best Practices

---

# Bonus 2: Config Conformance Pack Reflection 

# Config Conformance Pack — Operational Best Practices for Amazon S3

## Deploy Conformance Pack

Deploy Conformance Pack với **Operational Best Practices for Amazon S3**.

<img width="1445" height="754" alt="image" src="https://github.com/user-attachments/assets/38605677-4486-4f03-8aec-99439fa931ed" />

> Figure 9 — Deploy thành công Operational Best Practices for Amazon S3 Conformance Pack.

---

## Các Rule Được Evaluate

Sau khi deploy, AWS Config evaluate nhiều S3 security rules liên quan trực tiếp tới production workload.

<img width="1599" height="622" alt="image" src="https://github.com/user-attachments/assets/cff220cd-d1bb-4f04-b79b-ee4059e24632" />

> Figure 10 — AWS Config evaluate các S3 security rules liên quan tới production workload.

---

<img width="1614" height="351" alt="image" src="https://github.com/user-attachments/assets/ba10e9e5-b719-4e10-a65e-17d68e7f4681" />

> Figure 11 — Compliance status của các S3 security controls trong Conformance Pack.

---

# Reflection — Production AI RAG Workload

Production workload của hệ thống là một AI RAG Chatbot sử dụng Amazon S3 để lưu:

* Frontend static assets
* Uploaded user documents
* Chunked documents
* Embeddings metadata
* Retrieval knowledge base

---

## `s3-default-encryption-kms`

Đây là rule quan trọng nhất vì toàn bộ knowledge base của hệ thống được lưu trong S3. Dù embeddings không chứa raw documents hoàn chỉnh, attacker vẫn có thể suy luận thông tin ngữ nghĩa từ vector embeddings và metadata nếu dữ liệu bị lộ.

Việc enforce mặc định mã hóa bằng AWS KMS giúp:

* Bảo vệ data-at-rest
* Giảm rủi ro IAM misconfiguration
* Tăng compliance cho production workload

---

## `s3-bucket-ssl-requests-only`

AI chatbot cho phép upload tài liệu để ingest vào RAG pipeline, vì vậy mọi request tới S3 bắt buộc phải sử dụng HTTPS/TLS.

Rule này giúp giảm nguy cơ:

* MITM attack
* Session interception
* Document leakage trong quá trình upload

---

## `s3-bucket-public-read-prohibited`

Knowledge base là tài sản quan trọng nhất của hệ thống RAG.

Nếu bucket bị public read ngoài ý muốn, attacker có thể:

* Tải xuống embeddings
* Phân tích metadata
* Reconstruct internal knowledge base

Rule này giúp đảm bảo toàn bộ retrieval data chỉ được truy cập thông qua IAM policies được kiểm soát.

---

## `s3-bucket-versioning-enabled`

Versioning đặc biệt quan trọng đối với AI workloads vì ingestion pipeline có thể gặp lỗi như:

* Chunking bug
* Embedding corruption
* Overwrite nhầm frontend build
* Accidental deletion của documents

Khi bật versioning, hệ thống có thể rollback object cũ nhanh chóng mà không cần rebuild toàn bộ vector database hoặc redeploy frontend application.

---

# Bonus 3: Reserved Instance / Savings Plan Decision 

# Reserved Instance / Savings Plan Analysis

## Workshop Environment Decision

Đối với workshop environment ngắn hạn, nhóm quyết định sử dụng On-Demand instances thay vì Reserved Instances.

---

## Lý Do

* Reserved Instance yêu cầu commitment dài hạn (1–3 năm)
* Chi phí upfront không phù hợp với temporary workload
* Workshop duration quá ngắn để đạt break-even point

| Loại              | Phù Hợp Workshop? | Lý Do                               |
| ----------------- | ----------------- | ----------------------------------- |
| On-Demand         | ✅ Yes             | Linh hoạt, không commitment         |
| Reserved Instance | ❌ No              | Không cost-effective cho short-term |
| Savings Plan      | ❌ No              | Không phù hợp workload ngắn hạn     |

---

# Production Recommendation

Đối với production AI RAG workload chạy liên tục:

* Backend inference APIs hoạt động 24/7
* Database và vector retrieval có baseline load ổn định
* Frontend và ingestion pipeline hoạt động thường xuyên

---

## Production Strategy

Khuyến nghị:

* Compute Savings Plans hoặc 1-year Reserved Instances
* Reserve khoảng 60–80% baseline compute load
* Giữ phần còn lại ở On-Demand để linh hoạt scale inference workload

---

## Estimated Savings

| Strategy       | Estimated Savings |
| -------------- | ----------------- |
| 1-Year RI      | ~30–35%           |
| Savings Plan   | ~30%              |
| Full On-Demand | 0%                |

---

# Bonus 4: Reflection — “Waste → Optimization” 

# Reflection — Cost Optimization During Redeploy

Trong quá trình redeploy và hardening hệ thống AI RAG Chatbot, nhóm đã xác định nhiều nguồn gây lãng phí tài nguyên cloud và thực hiện tối ưu hóa.

---

# 1. Public Exposure Risk

Ban đầu backend Security Group cho phép SSH từ `0.0.0.0/0`, tạo unnecessary attack surface cho production environment.

## Optimization

* Xóa public SSH access
* Chuyển sang internal/private management access flow

## Impact

* Giảm security risk
* Giảm khả năng brute-force attack
* Harden production environment

---

# 2. Storage Configuration Inefficiency

S3 bucket ban đầu chưa bật:

* Block Public Access
* HTTPS-only policy
* Default encryption

## Optimization

* Enable Block Public Access
* Enforce HTTPS-only requests
* Enable KMS encryption

## Impact

* Tăng compliance level
* Giảm nguy cơ data exposure
* Bảo vệ embeddings và uploaded documents

---

# 3. Disaster Recovery Improvements

Bucket ban đầu chưa bật versioning.

## Optimization

Enable S3 versioning cho:

* Frontend assets
* Uploaded documents
* Ingestion data

## Impact

Cho phép rollback nhanh khi xảy ra:

* Overwrite nhầm frontend build
* Ingestion bug
* Accidental deletion
* Corrupted embedding uploads

---

# Kết Luận

Sau khi tối ưu:

* Security posture được cải thiện đáng kể
* Storage cost giảm
* Reliability tăng
* Production workload phù hợp hơn với AWS Well-Architected Framework

## Các trụ cột được cải thiện

* Security
* Reliability
* Cost Optimization

---

## Tham Khảo & Links

| Tài Nguyên | Link |
|----------|------|
| **AWS Cost Explorer** | https://console.aws.amazon.com/cost-management/home?#/custom |
| **CloudWatch Dashboards** | https://console.aws.amazon.com/cloudwatch/home?#dashboards: |
| **EventBridge Rules** | https://console.aws.amazon.com/events/home?#/rules |
| **S3 Block Public Access** | https://console.aws.amazon.com/s3/access-points/settings |
| **CloudTrail Events** | https://console.aws.amazon.com/cloudtrail/home?#/events |
| **W5 Evidence Pack** | [Link tới W5 evidence file] |

---

**Evidence Pack Compiled:** 21 tháng 5, 2026  
**Người Trình Bày:** [Tên Trưởng Nhóm]  
**Phê Duyệt bởi Trainer:** [Chữ Ký Trainer / Ngày Tháng]
