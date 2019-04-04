# [Thinking in React](https://reactjs.org/docs/thinking-in-react.html)

> Theo quan điểm của chúng tôi, React, là một cách tốt để xây dựng những ứng dụng web với JavaScript. Nó đã mở rộng rất tốt cho các sản phẩm Facebook và Instagram.

Một trong những phần tuyệt vời nhất của React là cách khiến bạn nghĩ về ứng dụng như bạn xây chúng (kiểu Lego vậy). Trong hướng dẫn này, chúng ta sẽ tham khảo các bước để xây dựng: 1 ứng dụng tìm kiếm sản phẩm.

## Bắt đầu với bản thiết kế

Giả sử rằng chúng ta đã có một đoạn dữ liệu JSON và một bản giao diện mẫu dưới đây

![Ví dụ](https://reactjs.org/static/thinking-in-react-mock-1071fbcc9eed01fddc115b41e193ec11-4dd91.png)

JSON API trả về  sẽ trông như này

```javascript
[
  {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},
  {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},
  {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},
  {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},
  {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},
  {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}
];
```

## Bước 1: chia giao diện thành 1 hệ thống những component

Bạn sẽ muốn vẽ những chiếc hộp bao quanh mỗi component (và cả subcomponent nữa) trong bản nháp và đặt tên cho chúng. Nếu bạn làm việc với một designẻ, họ có thể đã hoàn thành việc đó, nên cứ nói với họ xem! Những tên layer trong photoshop có thể được dùng như là các React Component!

Nhưng làm thế nào để bạn biết cái nào nên là component? Hãy làm y như khi bạn quyết định tạo 1 function hay 1 object mới. Một trong những kỹ thuật đó là [Single Respónibility Principle](https://en.wikipedia.org/wiki/Single_responsibility_principle). Đúng vậy, một Component chỉ nên làm một việc duy nhất, nếu Component đó mở rộng, thì ta nên chia nó thành các component phụ.

Vì chúng ta thường sử dụng JSON để hiện thị dữ liẹu tới usẻ, nên bạn sẽ thấy rằng nếu dữ liệu được xây đúng, thì UI (user interface) cũng sẽ tương ứng hoàn hảo, bởi vì UI và Data Model có xu hướng tuân thủ cùng một *cấu trúc thông tin*, nghĩ là việc chia giao diện thành các Component thường không đáng kể. Chỉ cần chia nó thành các Component đại diện chính xác cho một phần của Data Model.

![Chia vùng](https://reactjs.org/static/thinking-in-react-components-eb8bda25806a89ebdc838813bdfa3601-82965.png)

Ở hình trên, chúng ta có 5 Component trong ứng dụng mà chúng ta sẽ xây. Chữ in nghiêng đại diện cho dữ liệu mà mỗi Component mô tả:

1. FilterableProductTable (màu cam): chứa toàn bộ ví dụ
2. SearchBar (màu xanh da trời): nhận tất cả các *user input*
3. ProductTable (màu xanh lá cây): hiển thị và lọc *data collection* dựa trên *user input*
4. ProductCategoryRow (màu ngọc lam): hiển thị tiêu đề cho mỗi *category*
5. ProductRow (red): hiển thị một dòng dữ liệu, đại diện cho từng *product*

Nếu bạn nhìn vào 'ProductTable', bạn sẽ thấy tiêu đề (bao gồm "Name" và "Price") không phải thành phần của chính nó. Trong ví dụ này thì chúng ta sẽ để tiêu đề đó như là một phần của 'ProductTable' bởi vì nó là một phần của việc render *data collection* mà component 'ProductTable' chịu trách nhiệm. Tuy nhiên, nếu header này phát triển phức tạp hơn (như là chứng ta muốn thêm chức năng sắp xếp), thì nên có một sub-component mới, 'ProductTableHeader' component chẳng hạn.

Bây giờ chúng ta đã xác định các Component trong bản thiêt kế, hãy sắp xếp chúng thành một hệ thống phân cấp (hierarchy). Các Component mà xuất hiện bên trong Component khác trong bản thiết kế, thì nên xuất hiện như là một nhánh con của hệ thống phân cấp

- FilterableProductTable
  - SearchBar
  - ProductTable
    - ProductCategoryRơ
    - ProductRow

## Bước 2: Xây dựng một phiên bản tĩnh với React (a static version in React)

Xem [ví dụ](https://codepen.io/gaearon/pen/BwWzwm) với [Codepen](https://codepen.io/)

Bây giờ bạn đã có hệ thống phân cấp các Component, hãy bắt đầu triển khai ứng dụng. Các dễ nhất là xây một phiên bản mà lấy dữ liệu và render ra giao diện nhưng không có sự tương tác (như thao tác nhập xóa input, bấm nút, ...). Cách này là tốt nhất vì khi xây dựng một static version như vậy, chỉ cần yêu cầu gõ rất nhiều và không cần suy nghĩ, nhưng khi thêm tính tương tác vào ứng dụng, lại yêu cầu rất nhiều suy nghĩ và không cần gõ nhiều lắm đâu. Chúng ta sẽ hiểu vi sao.

Để  build 1 static version mà render ra dữ liệu, bạn sẽ muốn build các Component mà reuse các Component khác and truyền data thông qua *props* (một cách để truyền data từ Parent Component đến Child Component). Nếu bạn đã quen với khái niệm *state* thì **đừng dùng state trong static version**. State được dùng chỉ với mục đích tương tác, nghĩa là, dữ liệu thay đổi theo thời gian. Chúng ta không cần đến state ở static version.

Bạn có thể xây từ trên xuống dưới và ngược lại. nghĩa là bạn vừa có thể bắt đầu với component ở vị trí cao trong hệ thống phân cấp (như là FilterableProductTable), hoặc là với component ở vị trí thấp (ProductRow). Trong những ví dụ đơn giản, sẽ thường được bắt đầu theo cách từ trên xuống dưới, trong những dự án lớn hơn, sẽ là dễ dàng hơn với cách từ dưới lên trên và viết test những gì bạn vừa build.

Khi kết thúc bước 2 này, bạn sẽ có một thư viện bao gồm các Component có thể  hiển thị ra dữ liệu. Những Component chỉ có phương thức render() trong phiên bản static này. Component ở trên cùng của hệ thống phân cấp (FilterableProductTable) sẽ lấy dữ liệu của bạn như một props, Nếu bạn thay đổi dữ liệu và gọi ReactDOM.render() lại lần nữa, giao diện sẽ được cập nhật. Sẽ rất dễ dàng để nhìn giao diện được update và nhxưng chỗ tạo nên thay đổi khi mà không có gì phức tạp xảy ra, React's one-way data flow sẽ giữ mọi thứ ổn định.

Hãy tham khảo [tài liệu React](https://reactjs.org/docs/) nếu bạn cần giúp đỡ thực hiện bước này

## Chú ý: Props và State

Có hai loại dữ liệu trong React: props và state, rất quan trọng để hiểu sự khác biệt giữa chúng, lướt qua [đây](https://reactjs.org/docs/interactivity-and-dynamic-uis.html) nếu bạn không chắc sự khác nhau là gì

## Bước 3: Xác định sự tối thiểu (nhưng hoàn toàn) mô tả giao diện

Để làm giao diện có tính tương tác (interactive), bạn cần có khả năng kích hoạt sự thay đổi đến dữ liệu nền, React có thể xử lý dễ dàng với **State**.

Để xây dựng ứng dụng chính xác, điều đầu tiên bạn cần là nghĩ về số lượng nhỏ nhất của tập hợp state mà ứng dụng cần. Bí kíp là đây [DRY: Don't Repeat Yourself](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself). Tìm ra số lượng nhỏ nhất state cần thiết để mô tả ứng dụng và tính toán mọi thứ còn lại bạn cần theo yêu cầu. Ví dụ: bạn xây 1 TODO list, chỉ cần giữ một mảng các TODO items, đừng giữ 1 biến state cho việc đếm, thay vào đó, đơn giản chỉ cần lấy length của mảng khi render ra giao diện, sẽ được số lượng TODO cần làm

Nghĩ về tất cả các chỗ có dùng dữ liệu trong ứng dụng. Chúng ta có:

- Danh sách sản phẩm khi ứng dụng render lần đầu tiên (the original list of products). **(1)**
- Cụm từ tìm kiếm mà người dùng nhập vào (the search text the usẻ has entered). **(2)**
- Giá trị của checkbox (the value of the checkbox). **(3)**
- Danh sách tìm kiếm của sản phẩm (the filtered list of products). **(4)**

Hãy xem xét và tìm ra những dữ liệu nào nên là state. Chỉ cần hỏi 3 câu hỏi cho mỗi chỗ dùng dữ liệu trên:

1. Dữ liệu đó có được truyền từ 'Parent Component' thông qua props không? nếu có, nó chắc chắn không phải là state.
2. Dữ liệu đó không đổi theo thời gian? nếu không đổi, nó chắc chắn không phải là state.
3. Bạn có thể tính toán ra nó dựa trên các state or props khác trong Component không? nếu có, nó không phải là state

Cùng phân tích, **(1)** được truyền như là props, vậy nên nó không phải là state. **(2)** và **(3)** dường như là state vì chúng thay đổi theo thời gian và không được tính từ bất cứ state khác nào. Và cuối cùng, **(4)** không phải là state vì nó được tính toán dựa trên sự kết hợp của **(1)**, **(2)** và **(3)**

## Bước 4: Xác định nên đặt State ở đâu

Xem ví dụ [bước 4](https://codepen.io/gaearon/pen/qPrNQZ) tại [Codepen](https://codepen.io/)

Vậy là chúng ta đã xác định số lượng state nhỏ nhất cho ứng dụng. Tiếp theo, chúng ta sẽ cần xác định những Component nào thay đổi, hay nói cách khác, sở hữu state.

Hãy nhớ là: React là một luồng dữ liệu một chiều của hệ thống phân cấp Component. Nó có thể không rõ ràng về việc Component nào nên sở hữu state. Vấn đề này là thử thách nhất đối với những newbie để hiểu, vậy nên hãy theo các bước sau để tìm ra nó.

Đối với mỗi thuộc tính của state trong ứng dụng:

- xác định từng Component mà render ra giao diện dựa trên key state đó.
- Tìm ra Component sở hữu chung (là một Component ở trên tất cả các Component mà cần state đó trong hệ thống phân cấp)
- Hoặc là Component sở hữu chung hay một Component khác cao hơn trong hệ thống phân cấp nên sở hữu state.
- Nếu bạn không thể tìm ra một Component mà nó có khả năng sở hữu state, tạo một Component mới để lưu state và thêm nó vào đâu đó trong hệ thống phân cấp ở dưới Component sở hữu chung.

Hãy áp dụng những điều trên với ứng dụng của chúng ta:

- 'ProductTable' cần lọc danh sách sản phẩm dựa trên state và 'SearchBar' cần để hiển thị từ khóa tìm kiếm và checked state
- Component sở hữu chung là FilterableProductTable.
- Sẽ có ý nghĩa khi để filter text và giá trị check trong Component FilterableProductTable

Vậy là chúng ta đã quyết định State sẽ được đặt trong FilterableProductTable Component. Đầu tiên, chúng ta thên một thuộc tính vào hàm khởi tạo (constructor) của Component FilterableProductTable để  miêu tả state khởi tạo của ứng dụng (initial state):

```javascript
  this.state = {
    filterText: '',
    inStockOnly: false,
  }
```

Tiếp đó chúng ta truyền 'filterText' và 'inStockOnly' tới các Component: ProductTable và SearchBar dưới dạng props.

```javascript
  //...
  <ProductTable filterText={this.state.filterText} />

  //...
  <SearchBar inStockOnly={this.state.inStockOnly} />
```

Cuối cùng, chúng ta dùng những props đó để lọc kết quả trong ProductTable và set giá trị của form fields trong SearchBar

```javascript
  //...
  const ProductTable = (props) => {
    const result = filterDateBaseOnProps(props.something);
    return (
      //...
      <div>{result}</div>
    )
  }
  //...
  const SearchBar = (props) => {
    return (
      <div>props.filterText</div>
    )
  }
```

Bạn có thể thử bằng cách set filterText là 'ball' và refresh lại app, bạn sẽ thấy kết quả tìm kiếm được update.

## Bước 5: Thêm luồng dữ liệu nghịch đảo

- Có thể hiểu là sự tương tác giữa Child Component với Parent Component (ở trên là dữ liệu đi từ Parent Component đến Child Component thông qua props)

xem ví dụ [bước 5](https://codepen.io/gaearon/pen/LzWZvb) tại [codepen](https://codepen.io/)

Cho đến nay, chúng ta đã xây 1 ứng dụng mà render, truyền props và state từ trên xuống dưới trong hệ thống phân cấp. Bây giờ là thời gian để hỗ trợ luồng dữ liệu theo hướng khác: form Components ở dưới sâu trong hệ thống phân cấp cần update state ở trong FilterableProductTable Component.

React làm cho luồng dữ liệu rõ ràng để hiểu ứng dụng vận hành thế nào, nhưng nó yêu cầu gõ nhiều hơn là two-way data binding.

Nếu bạn thử gõ hoặc tick vào checkbox trong phiên bản hiện tại của ứng dụng, bạn sẽ thấy rằng React sẽ không cập nhật tương ứng với thao tác của bạn. Đây là một chủ ý, khi chúng ta đã set giá trị của input luôn tương đường với state được truyền từ FilterableProductTable Component.

Hãy nghĩ về những gì chúng ta muốn xảy ra. Chúng ta muốn chắc rằng bất cứ khi nào user thay đổi forms, chúng ta sẽ update state để biểu diễn những gì người dùng thao tác. Vì các Component chỉ nên cập nhật State cúa chính chúng, FilterableProductTable sẽ truyền một hàm callback tới SearchBar, callback này sẽ thực thi bất cứ khi nào state nên được update. Chúng ta có thể dùng sự kiện **onChange** trong inuts để thông báo về nó. Hàm callback sẽ được truyền bởi FilterableProductTable sẽ gọi **setState()** , và ứng dụng sẽ được update

Nghe thì phức tạp, chúng thực sự chỉ là một vài dòng code, và chúng thưc sự rõ ràng trong việc mô tả luồng dữ liệu di chuyển trong ứng dụng.

## Tổng kết

Hy vọng rằng hướng dẫn này sẽ giúp bạn có một ý tưởng để xây dựng các Component và ứng dụng web với React. Có thể nó sẽ tốn nhiều công gõ hơn so với cách thức cũ bạn từng làm, nhớ rằng code được đọc nhiều hơn so với nó được viết, và nó cực kỳ dế dàng để đọc code theo chuẩn module, rõ ràng. Như là bạn bắt đầu xây dựng 1 thư viện lớn các Component, bạn sẽ đánh giá caoo tính rõ ràng và đóng gói này, và với những đoạn mã tái sử dụng, số lượng dòng code của bạn sẽ giảm thôi. :)
