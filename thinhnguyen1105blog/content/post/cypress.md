---
title: "Làm Automation Test như phim hoạt hình ? 😼"
date: 2021-02-11T19:53:14+07:00
description: 'Cho ai chưa biết Automation Test thì nó là một công cụ để giúp kiểm tra một hệ thống'
image: '/image/posts/cypress/automation-test.jpg'
tags: ["Testing"]
---

Cho ai chưa biết Automation Test thì nó là một công cụ để giúp kiểm tra một hệ thống có hoạt động chính xác như mình mong muốn không (giống như thầy giáo đi chấm bài ấy hề hề )

# 1. WHY AUTOMATION TEST ? 

Chả là dạo gần đây khi dự án mình đang làm đã gần đi vào ổn định ( và mong muốn nó luôn ổn định trong tương lai ) nên mình đã đi học đòi các bác trên mạng làm automation test để xem mặt mũi em nó như thế nào.

Chuyện là trước khi làm thì có nghe qua Selenium khá là nổi tiếng mà trong giới công nghệ các bác truyền tai nhau. Nhưng khi implement vào dự án thì cũng khá lằng nhằng phức tạp nên thôi bỏ vì 2 lí do, 1 là nó phải code bằng javascript và 2 là nó phải dễ code để tester bên mình có thể học đc =)) vậy là tiếp tục dev front-end mà k có automation, sai rồi lại sửa. 

Khoảng 2 tháng sau trong một hôm rảnh rỗi thì mình xem đc 1 clip về Cypress. Lạy chúa đây chính là thứ mà mình đã tìm kiếm bấy lâu nay ( thứ nhất là code bằng javascript và thứ hai là nó có UI quá đẹp và preview như phim hoạt hình ). Và thế là mình quyết định dùng luôn em nó

# 2.HOW TO INSTALL ?

Cài đặt cũng khá là đơn giản

 Bước 1:  ở terminal thì cài đặt cypress bằng lệnh ( nhớ cài node.js vào máy trước thì mới chạy đc npm )

```jsx
npm install cypress --save-dev
```

 Đúng là tool automation nên chạy install cũng automation luôn cực tiện

OK, install xong thì chạy lệnh để mở cypress

```jsx
./node_modules/.bin/cypress open
```

 Sau khi cypress chạy, nó sẽ mở lên 1 tab chrome để chạy automation test, nhớ allow cho nó có quyền truy cập nhé !

 Vậy là đã xong, trong cypress có rất nhiều modules đã được viết sẵn. Bạn chỉ cần click vào đó và xem thử cách nó hoạt động như thế nào. Sau khi chọn một test case trong integration test thì chỉ việc ấn run là nó sẽ chạy và chỉ việc ngồi xem hoạt hình thui =))) 

{{< youtube VvLocgtCQnY >}}

# 3. EVALUATE

Sau một thời gian đọc document và tìm hiểu em nó thì mình thấy em nó khá là tiện và mình nghĩ ai cũng có thể viết được. Cụ thể, các điểm mạnh của em nó như sau

## 1. Query element

Đại khái là mình sẽ có một bộ query để tìm ra 1 hoặc nhiều element xuất hiện trên trang web ( VD: button, input ...), điểm mạnh của Cypress là bạn có thể tìm kiếm theo nội dung hiển thị hoặc các thuộc tính của thẻ

```jsx
// Tìm kiếm theo nội dung
cy.contains('Đăng nhập')
// Tìm kiếm theo attributes
cy.get(`[data-test*=${selector}]`, ...args);
cy.getBySel("list-skeleton").should("not.exist");
cy.get('[type=email]')
ct.get('[type=text').eq(5)
```

## 2. Interactive

Vậy là sau khi đã chọn được chính xác thẻ cần tương tác thì mình sẽ phải bắt đầu tương tác, thường thì trên website sẽ là nhập vào ô input hoặc là click ( cypress support tất cả các dạng tương tác khác như scroll , checkbox nhưng để đơn giản mình sẽ chỉ liệt kê 2 cái hay dùng nhất thui nha )

```jsx
// click 
cy.get('.action-labels>.label').click({ multiple: true })
// input
cy.get('.action-email')
      .type('fake@email.com').should('have.value', 'fake@email.com')
```

## 3. Assertion

Sau khi tương tác xong thì mình sẽ cần đánh giá kết quả sau khi thực hiện hành động đó có đúng hay không, nếu đúng thì pass test case còn nếu sai thì nó sẽ báo lỗi nhé =)) 

```jsx
cy.get('.assertion-table')
.find('tbody tr:last')
// finds first <td> element with text content matching regular expression
.contains('td', /column content/i)
.should('be.visible')
```

Ngoài ra Cypress có hỗ trợ theo dõi API, theo dõi đường dẫn và nhiều chức năng khác nữa mà mình thấy khá thú vị. Bài này mình chỉ giới thiệu sơ qua về công cụ, ai muốn tìm hiểu sâu hơn thì có thể lên trang chủ của [Cypress]({{< ref "https://www.cypress.io/" >}} "Cypress") để đọc document nhé ;)
