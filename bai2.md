# BÀI 2: TỐI ƯU PROMPT SINH DOCSTRING & GIẢI THÍCH LOGIC

## 1. Phân tích lỗ hổng của prompt thô sơ của thực tập sinh
*   **Không có vai trò & ngữ cảnh nghiệp vụ:** AI không biết mục đích sử dụng của hàm `cD`, không hiểu các ký hiệu viết tắt như `a`, `b`, `c` là gì trong hệ thống nên chỉ có thể dịch nghĩa đen của code.
*   **Ràng buộc lỏng lẻo:** Không yêu cầu chuẩn tài liệu cụ thể (ví dụ: Javadoc).
*   **Nguy cơ dịch sai nghiệp vụ:** Do đặt tên biến tối nghĩa, AI dễ đoán sai logic nghiệp vụ (ví dụ: coi `b` là số lượng sản phẩm thay vì cấp độ khách hàng VIP/Gold).

## 2. Prompt tối ưu mới (Sử dụng cấu trúc 5 thành phần)
```markdown
Vai trò: Bạn là một Senior Java Developer kiêm Business Analyst chuyên nghiệp.

Ngữ cảnh: Hệ thống TechShop của chúng tôi có một hàm cũ tên là `cD` dùng để tính toán chiết khấu đơn hàng. Hàm này viết tắt và đặt tên biến vô nghĩa, hoàn toàn thiếu tài liệu.
Quy ước nghiệp vụ của các biến trong hệ thống như sau:
- Tham số `a` đại diện cho: Giá trị đơn hàng gốc (đơn vị: VNĐ).
- Tham số `b` đại diện cho: Cấp độ thành viên của khách hàng. Trong đó: 1 tương ứng với cấp độ Silver, 2 tương ứng với cấp độ Gold, các giá trị khác là Bronze (khách hàng thường).
- Tham số `c` đại diện cho: Mã giảm giá vận chuyển (Voucher Freeship). Nếu c = true tức là khách được miễn phí vận chuyển.

Nhiệm vụ:
1. Đọc hiểu đoạn mã nguồn Java được cung cấp dưới đây.
2. Đổi tên hàm và các biến bên trong hàm sang tiếng Anh có nghĩa, rõ ràng theo chuẩn Clean Code.
3. Viết Javadoc đầy đủ cho hàm (mô tả mục đích, ý nghĩa các tham số @param, giá trị trả về @return).
4. Viết comment inline giải thích chi tiết logic nghiệp vụ tại các dòng tính toán phức tạp.

Ràng buộc:
- Giữ nguyên logic xử lý nghiệp vụ gốc của đoạn code dưới đây, không được thay đổi kết quả tính toán chiết khấu.
- Trả về mã nguồn Java đã refactor hoàn chỉnh đặt trong code block.

Đoạn code gốc cần xử lý:
public double cD(double a, int b, boolean c) {
    double d = a;
    if (b == 2) {
        d = a * 0.9;
    } else if (b == 1) {
        d = a * 0.95;
    }
    if (c) {
        d = d - 30000;
    }
    return d < 0 ? 0 : d;
}
```

## 3. Kết quả mã nguồn đã có Javadoc và comment do AI sinh ra
```java
/**
 * Calculates the final payment amount for an order after applying member discounts 
 * and shipping vouchers.
 * 
 * @param originalAmount      the original order value in VND (must be non-negative)
 * @param membershipLevel     the customer's membership tier: 
 *                            1 for SILVER (5% discount), 
 *                            2 for GOLD (10% discount), 
 *                            any other value represents BRONZE (no discount)
 * @param hasFreeshipVoucher  flag indicating if the customer applies a freeship voucher 
 *                            (subtracts 30,000 VND from the final total)
 * @return the final calculated order amount after all promotions are applied, guaranteed 
 *         to be at least 0.
 */
public double calculateFinalAmount(double originalAmount, int membershipLevel, boolean hasFreeshipVoucher) {
    double discountedAmount = originalAmount;

    // Apply membership tier discount: Gold gets 10% off, Silver gets 5% off
    if (membershipLevel == 2) {
        discountedAmount = originalAmount * 0.9; // 10% discount for Gold members
    } else if (membershipLevel == 1) {
        discountedAmount = originalAmount * 0.95; // 5% discount for Silver members
    }

    // Deduct standard shipping fee subsidy (30,000 VND) if freeship voucher is active
    if (hasFreeshipVoucher) {
        discountedAmount = discountedAmount - 30000;
    }

    // Prevent negative balances (if discount + voucher exceeds original order value)
    return discountedAmount < 0 ? 0 : discountedAmount;
}
```
