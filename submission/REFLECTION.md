# Reflection — Lab 19

**Tên:** Hoàng Ngọc Đăng Khoa
**Cohort:** K4 - Track 2
**Path đã chạy:** both (Docker & Lite)

---

## Câu hỏi (≤ 200 chữ)

> Trên golden set 50 queries, mode nào thắng ở loại query nào (`exact` /
> `paraphrase` / `mixed`), và tại sao? Khi nào bạn **không** dùng hybrid
> (i.e. khi nào pure BM25 hoặc pure vector là lựa chọn đúng)?

- **Thắng theo loại query:**
  - `exact` (96.7%): BM25 và Hybrid đồng dẫn đầu vì query chứa từ khóa kỹ thuật chính xác (verbatim), tín hiệu lexical đủ mạnh để tìm đúng văn bản.
  - `mixed` (100.0%): Hybrid thắng tuyệt đối (BM25: 97.0%, Semantic: 98.5%) nhờ RRF hợp nhất ưu thế của cả từ khóa chuẩn lẫn ngữ cảnh mở rộng.
  - `paraphrase`: BM25 đạt 33.3%, Hybrid 32.0%, Semantic 24.0% (với model nhẹ `bge-small-en-v1.5`). Khi nâng cấp model đa ngữ (`bge-m3`), Semantic sẽ chiếm ưu thế vượt trội do nắm bắt được từ đồng nghĩa/diễn đạt lại.
- **Khi không dùng Hybrid:**
  - **Chọn Pure BM25**: Khi tìm kiếm mã lỗi (`ERR_500`), SKU, ký hiệu đặc biệt, log ID cần exact-match, hoặc hệ thống yêu cầu độ trễ siêu thấp (< 2ms) và tiết kiệm RAM/CPU.
  - **Chọn Pure Vector**: Khi người dùng tìm kiếm theo ý niệm trừu tượng, truy vấn đa ngôn ngữ (cross-lingual), hoặc câu dài không có từ khóa xuất hiện trong văn bản.
  - Tránh Hybrid khi giới hạn tài nguyên tính toán vì Hybrid phải chạy song song cả sparse + dense retriever rồi rank lại, tốn chi phí gấp đôi.

---

## Điều ngạc nhiên nhất khi làm lab này

Sự kết hợp đơn giản của RRF ($1/(k + rank)$) không cần chuẩn hoá scale điểm số khác biệt giữa BM25 và Vector cosine similarity nhưng lại đem lại độ chính xác cao nhất và ổn định nhất. Ngoài ra, mức độ data leakage khi target encoding trên key có cardinality cao (`session_id`) làm train AUC vọt lên gần 1.00 trong khi test AUC chỉ 0.522 là minh chứng rất trực quan về việc model "học thuộc" thay vì khái quát hoá.

---

## Bonus challenge

- [ ] Đã làm bonus (xem `bonus/`)
- [ ] Pair work với: _<tên đồng đội nếu có>_
