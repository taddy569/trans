# [Thinking in React](https://reactjs.org/docs/thinking-in-react.html)

## Bắt đầu với bản thiết kế mẫu

- Bạn đã có một bản thiết kế giao diện mẫu từ Designer và một đoạn dữ liệu JSON khi gọi API

## Bước 1: Chia giao diện ra thành một sơ đồ cây Component

Mỗi Component chỉ nên làm một việc duy nhất, nếu nó làm nhiều việc hơn một, thì nên chia nhỏ Component đó thành các component con nhỏ hơn

Có một số thành phần trong giao diện không thực sự thuộc về một Component nào, nhưng vì nó đơn giản (ví dụ như tiêu đề tĩnh) nên ta có thể gộp chung vào Component, nhưng nếu ta muốn thêm chức năng (như sắp xếp theo thứ tự) thì ta nên chia nhỏ Component đó ra, như đã nêu ở trên

Sau khi xác định được các Component, đưa chúng vào sơ đồ cây: nếu component A xuất hiện bên trong component B, ta cho A là component con của B. Ví dụ:

- FilterableProductTable
  - SearchBar
  - ProductTable
    - ProductCategoryRow
    - ProductRow

## Bước 2: Thiết kế một phiên bản tĩnh với React trước

Bạn đã có Sơ đồ cây Component rồi, giờ thì bắt đầu triển khai ứng dụng, cách dễ nhất là xây một phiên bản tĩnh (nhưng chưa có tương tác vội)

Chia tiến trình này ra:

- xây phiên bản tĩnh yêu cầu gõ nhiều và không nghĩ
- thêm sự tương tác thì yêu cầu nghĩ nhiều hơn và không cần gõ nhiều lắm

Xây phiên bản tĩnh, code Component bằng cách dùng các Component khác, truyền data thông qua *props*

Lưu ý là ở phiên bản tĩnh thì không dùng *state* vì nó chỉ dùng khi data thay đổi theo thời gian

Có thể build theo 2 hướng tiếp cận

1. từ trên xuống dưới (từ cha đến con): thích hợp với những ứng dụng nhỏ
2. từ dưới lên trên (từ con đến cha): thích hợp với ứng dụng lớn và phức tạp, cùng với viết Test

Ở bước này, thì dữ liệu sẽ đi từ Parent Component xuống Child Component qua việc dùng *props*

## Bước 3: Xác định số lượng key nhỏ nhất của **State**

Để làm UI có tính 'động', bạn cần có khả năng thay đổi dữ liệu nền bên dưới, React sẽ giúp bạn với *state*

Đầu tiên, là nghĩ về số lượng key trong object State sẽ dùng, tìm những dữ liệu mà ứng dụng của bạn cần ít nhất, những thứ còn lại, thì tính toán ra từ chúng

Xác định những nơi cần dùng dữ liệu trong ứng dụng:

1. Danh sách sản phẩm ban đầu *(1)*
2. Từ khóa tìm kiếm mà người dùng nhập vào *(2)*
3. Giá trị của ô checkbox (check hay uncheck) *(3)*
4. Danh sách sản phẩm sau khi lọc *(4)*

Đi qua từng dữ liệu trong danh sách trên, áp dụng 3 điều sau để biết cái nào nên là *state*:

1. nó được truyền như *props* từ component cha, nếu có thì nó không phải là *state*
2. nó không thay đổi theo thời gian, nếu có thì nó không phải là *state*
3. bạn có thể tính dữ liệu đó ra dựa trên các *state* hay *props* khác không, nếu có thì nó không phải là *state*

*(1)* được truyền dưới dạng props => không là state
*(2)* và *(3)* có thể giống state là vì chúng thay đổi theo thời gian và không được tổng hợp tính toán từ bất cứ state hay props nào
*(4)* không phải là state vì nó dược tổng hợp từ *(1)*, *(2)* và *(3)*

Vậy State sẽ là: *(2)* và *(3)*

## Bước 4: Xác định nên đặt State ở đâu

Ở bước 3, chúng ta đã xác định dữ liệu nào nên là State, giờ chúng ta cần xác định nên đặt chúng ở đâu

Đối với mỗi state, chúng ta cần

- xác định các Component mà render ra cái gì đó dựa trên state đó
- tìm một Component chung nhất (component mà ở trên tất cả các component còn lại trong sơ đồ cây Component)
- component chung hay các component khác cao hơn trong sơ đồ cây nên là sở hữu state đó
- nếu bạn không tìm thấy component nào nên sở hữu state, tạo 1 component mới ở cao hơn cả component chung

## Bước 5: Thêm luồng dữ liệu ngược lại

Đôi khi ta thao tác với Child Component, và cần update State ở Component cha, chính là lúc để thêm 1 luồng dữ liệu ngược lại thông qua các event. Như vậy state sẽ được cập nhật dựa trên thao tác người dùng.