# Căn bản về rust 🦀

## Biến:
- Biến trong rust là bất biến (mặc định)
```rust
fn main() {
    let x = 5; // x là một biến bất biến
    // x = 10; <--- lỗi ở đây: x là bất biến, không thể thay đổi
    println!("{}", x); // x vẫn là 5
}
```
- Để có quyền thay đổi giá trị, trước tên biến thêm mut (viết tắt của mutable)
```rust
fn main() {
    let mut x = 5; // x là một biến có thể thay đổi
    x = 10; // x có thể được thay đổi
    println!("{}", x); // x là 10
}
```

## Shadowing:
- Shadowing là việc sử dụng tên biến đã được khai báo trước đó để khai báo một biến mới mà không ảnh hưởng đến biến cũ
```rust
fn main() {
    let x = 5;
    {
        let x = 10; // x được shadowing
        println!("{}", x); // x là 10
    } // sau khi đi qua khối ngoặc nhọn, x vẫn giữ nguyên giá trị ban đầu là 5
    println!("{}", x); // x vẫn là 5
}
```

## Mô hình ownership:
- Mỗi giá trị trong Rust chỉ có một biến được gọi là chủ sở hữu
- Chỉ có một chủ sở hữu tại một thời điểm
- Khi chủ sở hữu ra khỏi phạm vi hoạt động, giá trị sẽ bị xóa
```rust
fn main() {
    let x = String::from("hello"); // x là chủ sở hữu của giá trị "hello"
    // let y = x; x chuyển giao chủ sở hữu cho y và bị xoá
    // println!("{}", x); <--- lỗi ở đây: giá trị của x đã bị xoá, không thể in
    let y = x.clone(); // cách fix: tạo một bản sao của x và gán cho y
    println!("{}", y); // không bị lỗi
    println!("{}", x); // in được

    let s = String::from("hello"); // s là chủ sở hữu của chuỗi "hello"
    fn takes_ownership(some_str: String) {
        println!("{}", some_str);
    }
    takes_ownership(s); // s chuyển giao chủ sở hữu cho takes_ownership và bị xoá giá trị
    fn gives_ownership() -> String {
        let some_str = String::from("hello");
        some_str // trả về giá trị và chuyển giao chủ sở hữu cho người gọi
    }
    fn takes_and_gives_back(some_str: String) -> String {
        some_str
    }
    let s1 = gives_ownership(); // s là chủ sở hữu của giá trị "hello"
    let s2 = String::from("hello");
    let s3 = takes_and_gives_back(s2); // lấy quyền sở hữu của s2 và trả cho s3
    println!("{}, {}", s1, s3);

    fn calculate_length(some_str: String) -> (String, usize) {
        let length = some_str.len(); // tính độ dài chuỗi
        (some_str, length) // trả về giá trị và chuyển giao quyền sở hữu
    }
    let s1 = String::from("hello");
    let (s2, len) = calculate_length(s1); // s2 và len là chủ sở hữu của giá trị
    println!("Độ dài {} là {}", s2, len);
}
```

## References:
- Chỉ có thể tham chiếu cho phép sửa đổi cho một dữ liệu trong một phạm vi cụ thể
- Không thể có một tham chiếu cho phép sửa đổi nếu một tham chiếu bất biến đã tồn tại trong phạm vi
```rust
fn main() {
    fn calculate_length(some_str: &mut String) -> usize { // tham chiếu và cho phép sửa đổi
        some_str.push_str(" world!");
        let length = some_str.len(); // tính độ dài chuỗi
        length // trả về giá trị và chuyển giao quyền sở hữu
    }
    let s1 = String::from("hello");
    let len = calculate_length(&mut s1); // tham chiếu và cho phép sửa đổi đến s1
    println!("Độ dài {} là {}", s1, len);
}
```

## Stack và heap:
- ### Stack:
    - Stack là một bộ nhớ có kích thước cố định và ko thể thay đổi dung lượng

- ### Heap:
    - Heap là một bộ nhớ có kích thước không cố định và có thể thay đổi dung lượng trong quá trình chạy
```rust
fn main() {
    fn x() {
        // let a = "hello"; a được lưu trữ trên stack
        // a.push_str("!"); <--- lỗi ở đây: không thể thêm vào char trên stack
        let a = "hello".to_string(); // cách fix 
        let b = 100;
        y();
        println!("{}, {}", a, b);
    }
    fn y() {
        let mut a = String::from("World"); // a được lưu trữ trên heap
        a.push_str("!");
    }
    x();
}
```

## Kiểu dữ liệu:
- ### Kiểu dữ liệu cơ bản:
    - Integer:
      
        | Độ dài  | Có dấu (`i`) | Không dấu (`u`) |
        |---------|-------------|-----------------|
        | 8-bit   | `i8`       | `u8`            |
        | 16-bit  | `i16`      | `u16`           |
        | 32-bit  | `i32`      | `u32`           |
        | 64-bit  | `i64`      | `u64`           |
        | 128-bit | `i128`     | `u128`          |
        | arch | `isize`  | `usize`         |

    - Float:
      
        | Độ dài  | Kiểu dữ liệu |
        |---------|-------------|
        | 32-bit  | `f32`       |
        | 64-bit  | `f64`       |

    - Boolean:
      
        | Điều kiện | Giá trị |
        |----------|--------|
        | `true`   | Đúng   |
        | `false`  | Sai    |
        | `None`   | Không có |

    - Character:
        - Character cũng có thể là unicode
        ```rust
        fn main() {
            let x = 'a'; // x là một kí tự
            let y = '💀'; // y là một kí tự unicode
            println!("{}", x); // x là 'a'
            println!("{}", y); // y là '💀'
        }
        ```

- ### kiểu dữ liệu phức tạp:
    - Tuple:
        - Tuple là một tập hợp các phần tử có nhiều kiểu dữ liệu khác nhau
        ```rust
        fn main() {
            let x = (1, 2, 3); // x là một tuple có 3 phần
            let (a, b, c) = x; // x được giải phóng thành 3 biến
            let a = x.0; // a nhận giá trị của phần đầu tiên
            let b = x.1; // b nhận giá trị của phần thứ 2
            let c = x.2; // c nhận giá trị của phần thứ 3
            println!("{}, {}, {}", a, b, c); // in ra 1, 2, 3
        }
        ```

    - Array:
        - Array là một tập hợp có kích thước cố định và các phần tử có cùng kiểu dữ liệu
        ```rust
        fn main() {
            let x = [1, 2, 3]; // x là một array có 3 phần
            for i in x.iter() {
                    println!("{}", i);
            } // in ra từng phần của array
        }
        ```

    - Structs:
        - Structs là một tập hợp các phần tử có nhiều kiểu dữ liệu khác nhau và có thể có các phương thức
        ```rust
        fn main() {
            #[derive(Debug)]
            struct Point {
                x: i32,
                y: i32,
            }
            let p1 = Point {
                x: 1,
                y: 2,
            };
            let x1 = p1.x; // x nhận giá trị của phần x
            let y1 = p1.y; // y nhận giá trị của phần y
            println!("Point({}, {})", x1, y1); // in ra Point(1, 2)
            fn create_new_point(x: i32, y: i32) -> Point {
                Point {
                    x: x,
                    y: y,
                }
            }
            let p2 = create_new_point(3, 4);
            let x2 = p2.x;
            let y2 = p2.y;
            println!("Point({}, {})", x2, y2); // in ra Point(3, 4)
            // println!("{}", p2); <--- lỗi ở đây: không thể in được
            println!("{:#?}", p2); // cách fix: dùng trình debug
            let p3 = Point {
                x: 5,
                ..p2 // copy các phần tử còn lại từ p2
            };
            let x3 = p3.x;
            let y3 = p3.y;
            println!("Point({}, {})", x3, y3); // in ra Point(5, 4)
            struct hinh_chu_nhat {
                dai: u32,
                rong: u32,
            }
            fn dien_tich(kich_thuoc: &hinh_chu_nhat) -> u32 {
                kich_thuoc.dai * kich_thuoc.rong
            }
            let hinh_chu_nhat = hinh_chu_nhat {
                dai: 10,
                rong: 5,
            };
            println!("kích thước hình chữ nhật: {}", dien_tich(&hinh_chu_nhat));
            impl hinh_chu_nhat {
                fn dien_tich(&self) -> u32 {
                    self.dai * self.rong
                }
                fn chua(&self, hinh_chu_nhat_khac: &hinh_chu_nhat) -> bool {
                    self.dai > hinh_chu_nhat_khac.dai && self.rong > hinh_chu_nhat_khac.rong
                }
                fn hinh_vuong(kich_thuoc: u32) -> hinh_chu_nhat {
                    hinh_chu_nhat {
                        dai: kich_thuoc,
                        rong: kich_thuoc,
                    }
                }
            } // thêm phương thức vào struct
            println!("kích thước hình chữ nhật: {}", hinh_chu_nhat.dien_tich());
            let hinh_chu_nhat_khac = hinh_chu_nhat {
                dai: 8,
                rong: 3,
            };
            println!("hình chữ nhật có thể chứa hình chữ nhật khác: {}", hinh_chu_nhat.chua(&hinh_chu_nhat_khac));
        }
        ```
    
    - Enum:
        - Enum là một kiểu dữ liệu cho phép định nghĩa một tập hợp các giá trị cố định
        ```rust
        fn main() {
            #[derive(Debug)]
            enum ip_address_kind {
                IPv4(String),
                IPv6(String),
            }
            #[derive(Debug)]
            struct ip_address {
                kind: ip_address_kind,
                address: String,
            }
            impl ip_address {
                fn some_fn(){
                    println!("Hello, world!");
                }
            }
            let localhost = ip_address_kind::IPv4(String::from("127.0.0.1"));
            println!("localhost: {:#?}", localhost);
        }
        ```
        - Option enum:
            - Option enum là một kiểu dữ liệu cho phép định nghĩa một giá trị có thể là một trong hai giá trị: có hoặc không
            ```rust
            fn main() {
                let x = 5;
                let y = Some(5);
                // let z = x + y; <--- lỗi ở đây: không thể cộng số nguyên và Option
                let z = x + y.unwrap_or(0); // cách fix: nếu y là None thì dùng 0, còn lại dùng giá trị trong y
                println!("z: {}", z);
            }
            ```
    
    - Vector:
        - Vector là một kiểu dữ liệu cho phép lưu trữ một danh sách các giá trị có thể phóng to thu nhỏ
        ```rust
        fn main() {
            let v1 = vec![1, 2, 3];
            println!("v1: {:?}", v1);
            let mut v2 = Vec::new();
            v2.push(1);
            v2.push(2);
            v2.push(3);
            println!("v2: {:?}", v2);
            let two = &v1[1];
            println!("two: {}", two);
            match v1.get(1) {
                Some(three) => println!("two: {}", three),
                None => println!("None"),
            }
            for i in &v2 {
                println!("i: {}", i);
            } // in ra từng giá trị của v2
            for i in &mut v2 {
                *i += 10;
            } // cộng từng giá trị của v2 với 10
            for i in &v2 {
                println!("i: {}", i);
            } // in ra từng giá trị của v2
        }
        ```
        - Ví dụ với nữa enum
        ```rust
        fn main() {
            enum SheetCell {
                Int(i32),
                Float(f64),
                Text(String),
            }
            let row = vec![
                SheetCell::Int(1),
                SheetCell::Float(3.14),
                SheetCell::Text(String::from("hello")),
            ];
            match &row[0] {
                SheetCell::Float(f) => println!("{}", f),
                _ => println!("Not a float"),
            }
        }
        ```

    - String:
        - String là một kiểu dữ liệu cho phép lưu trữ chuỗi ký tự
        ```rust
        fn main() {
            use unicode_segmentation::UnicodeSegmentation;
            let s1 = String::from("hello");
            let s2 = String::new();
            let s3 = "hello".to_string();
            println!("{}", s1);
            println!("{}", s2);
            println!("{}", s3);
            // s4 = s1 + " world";
            // println!("{}", s1); <--- lỗi ở đây: giá trị của s1 đã bị chuyển giao quyền sở hữu
            let mut s4: String = String::new();
            s4 = s1.clone() + " world"; // cách fix: clone giá trị của s1
            println!("{}", s4);
            for i in s4.bytes() {
                println!("{}", i);
            }
            for i in s4.chars() {
                println!("{}", i);
            }
            for i in s4.graphemes(true) {
                println!("{}", i);
            }
        }
        ```

    - Hash map:
        - Hash map là một kiểu dữ liệu cho phép lưu trữ các cặp key-value
        ```rust
        fn main() {
            let mu = String::from("MU");
            let mc = String::from("MC");
            let mut scores = HashMap::new();
            scores.insert(mu, 10);
            scores.insert(mc, 20);
            scores.entry(mc).or_insert(30).get(); // nếu không có key thì thêm key và value
            let team_name = String::from("MU");
            let score = scores.get(&team_name);
            for (key, value) in &scores {
                println!("{}: {}", key, value);
            }
            println!("{:?}", score);
        }
        ```
    - ### Ép kiểu:
        - Rust không thể tự động ép kiểu như các ngôn ngữ khác
        - Để ép kiểu, ta phải sử dụng từ khoá `as` để chuyển đổi kiểu dữ liệu

        ```rust
        fn main() {
            let x = 5; // x: i32
            let y = 3.14;
            // let z = x + y; <--- lỗi ở đây: rust không thể tự động ép kiểu i32 sang f64
            let z = x as f64 + y; // cách fix: ép kiểu của x sang f64 rồi mới thực hiện phép cộng

        }
        ```

        - Cách khác là sử dụng hàm into

        ```rust
        fn main() {
            let x: i32 = 42;
            let y: f64 = x.into(); // Chuyển đổi i32 -> f64
        }
        ```

## Match:
- Match là một cách để kiểm tra giá trị của một biến và thực hiện một hành động dựa trên giá trị đó
```rust
fn main() {
    enum Coin {
        Bitcoin(Balance),
        Ethereum(Balance),
        Near(Balance),
        Solana(Balance),
    }
    #[derive(Debug)]
    enum Balance {
        Small,
        Intermediate,
        Fish,
        Shark,
    }
    fn decimals(coin: Coin) -> u32 {
        match coin {
            Coin::Bitcoin(bala) => {
                println!("Bitcoin match!");
                println!("I'm a {:#?}", bala);
                96_268
            }
            Coin::Ethereum(bala) => {
                println!("Ethereum match!");
                println!("I'm a {:#?}", bala);
                2_674
            }
            Coin::Near(bala) => {
                println!("Near match!");
                println!("I'm a {:#?}", bala);
                3
            }
            Coin::Solana(bala) => {
                println!("Solana match!");
                println!("I'm a {:#?}", bala);
                170
            }
        }
    }
    println!("Decimals of Bitcoin: {}", decimals(Coin::Bitcoin(Balance::Shark)));
}
```
- Ví dụ nữa với option enum
```rust
fn main() {
    let five = Some(5);
    let six = plus_one(five);
    println!("{:#?}", six);
    let none = plus_one(None);
    println!("{:#?}", none);
    fn plus_one(x: Option<i32>) -> Option<i32> {
        match x {
            Some(x) => Some(x + 1),
            _ => None, // nếu không khớp với bất kỳ giá trị nào trong match trả về None
        }
    }
}
```

## Crates và Modules:
- Crates là một tập hợp các module và các file có thể được sử dụng trong một dự án Rust
- Ví dụ cơ bản
    - file `lib.rs` 

    ```rust
    mod front_house {
        pub mod hosting {
            pub fn add_to_waitlist() {
                println!("Adding to waitlist...");
            }
            pub fn seat_at_table() {
                println!("Seating you at your table...");
            }
        }
        pub mod serving {
            pub fn take_order() {
                println!("Taking your order...");
            }
            pub fn take_payment() {
                println!("Processing payment...");
            }
        }
    }
    fn call_order() {
        println!("Calling order...");
    }
    mod back_house {
        pub struct Breakfast {
            pub toast: String,
            pub fruit: String,
        }
        impl Breakfast {
            pub fn monday(toast: &str) -> Breakfast {
                Breakfast {
                    toast: String::from(toast),
                    fruit: String::from("blueberries"),
                }    
            }
        }
        pub fn cook_order() {
            println!("Cooking your order...");
        }
        pub fn fix_order() {
            println!("Fixing your order...");
            super::call_order();
            cook_order();
        }
    }
    pub fn eat_at_restanrant() {
        crate::front_house::hosting::add_to_waitlist();
        crate::front_house::hosting::seat_at_table();
        let mut order = back_house::Breakfast::monday("fish");
        call_order();
        order.toast = String::from("chicken");
        back_house::fix_order();
        crate::front_house::serving::take_order();
        crate::front_house::serving::take_payment();
    }
    ```

    - file `main.rs`

    ```rust
    use crate::eat_at_restanrant;

    fn main() {
        eat_at_restanrant();
    }
    ``` 

## Packages:
- Trong file `Cargo.toml` thêm dưới dòng `[dependencies]`:
```toml
# trong ví dụ này ta dùng thư viện hex
hex = "0.4.3"
```
- Trong file `main.rs` thêm
```rust
use hex
```

## Generic Types:
- Generic types cho phép ta sử dụng cùng một hàm với nhiều loại dữ liệu khác nhau
```rust
fn main() {
    let number_list = vec![1, 2, 3, 4, 5];
    let char_list = vec!['a', 'b', 'c', 'd', 'e'];
    fn get_largest<T: PartialOrd + Copy>(list: &[T]) -> T {
        let mut largest = list[0];
        for &item in list.iter() {
            if item > largest {
                largest = item;
            }
        }
        largest
    }
    println!("{}", get_largest(&number_list));
    println!("{}", get_largest(&char_list));
}
```
- Ví dụ nữa với structs 
```rust
fn main() {
    struct Point<T, U> {
        x: T,
        y: U,
    }
    impl <T, U> Point<T, U> {
        fn mixed<V, W>(self, orther: Point<V, W>) -> Point<T, W> {
            Point {
                x: self.x,
                y: orther.y,
            }
        }
    }
    let p1 = Point{
        x: 5,
        y: 10,
    };
    let p2 = Point{
        x: 3.14,
        y: 4.13,
    };
    let p3 = p1.mixed(p2);
    println!("p3.x = {}, p3.y = {}", p3.x, p3.y);
}
```

## Trait:
- Trait là một tập hợp các phương thức có thể được thực hiện bởi một loại dữ liệu 
```rust
fn main() {
    struct Data {
        num1: i32,
        num2: i32,
        str1: String,
        optional: Option<i32>,
    }
    struct Data2 {
        num1: i32,
        num2: i32,
        str1: String,
        optional: Option<i32>,
    }
    impl Data {
        fn new(num1: i32, num2: i32, str1: String) -> Data {
            Data {
                num1,
                num2,
                str1,
                optional: None,
            }
        }
    }
    trait Transform {
        fn revert(&self) -> String {
            println!("Revert Data..."); // hàm mặc định
        };
    }
    impl Transform for Data {
        fn revert(&self) -> String {
            self.str1.chars().rev().collect::<String>()
        }
    }
    impl Transform for Data2 {
        fn revert(&self) -> String {
            (self.num1 + self.num2).to_string()
        }
    }
    let a = Data::new(1, 2, "Hello".to_string());
    println!("{}", a.revert());
    let b = Data2 {
        num1: 1,
        num2: 2,
        str1: "Hello".to_string(),
        optional: None,
    };
    println!("{}", b.revert());
}
```

## Lifetime:
- Lifetime đảm bảo rằng tham chiếu đến dữ liệu luôn hợp lệ trong suốt thời gian nó được sử dụng
```rust
fn main() {
    let a = 5;
    let b = 4;
    let c = get_ref(&a, &b);
    println!("{}", c);
    // fn get_ref() -> &i32 {
    //     let a = 5;
    //     &a <--- lỗi ở đây: giá trị của a đã bị xóa khi ra ngoài hàm
    // }
    fn get_ref<'a>(param1: &'a i32, param2: &'a i32) -> &'a i32 { // cách fix: dùng lifetime
        if param1 > param2 {
            param1
        } else {
            param2
        }
    }
}
```

## Error handle và result:
- Error handle: cách xử lý lỗi trong Rust
- Result: kết quả có thể là thành công hoặc thất bại
```rust
fn main() {
    // panic!("Error"); để thông báo lỗi, dùng panic!()
    enum Result<T, E> {
        Ok(T),
        Err(E),
    }
    fn try_login() -> Result<i32, String> {
        Ok(1);
        // Err("Login error".to_string())
    }
}
```
- Ví dụ nữa với structs
```rust
fn main() {
    struct Employee {
        position: Position,
        status: Status,
    }
    enum Position {
        IT,
        CEO,
        CTO,
        Manager,
        Marketer,
    }
    enum Status {
        Active,
        Denied,
    }
    fn try_access(employee: &Employee) -> Result<(), String> {
        match employee.status {
            Status::Denied => return Err("Access denied".to_owned()),
            _ => (),
        }
        match employee.position {
            Position::CEO => Ok(()),
            Position::CTO => Ok(()),
            Position::Manager => Ok(()),
            _ => Err("invalid position".to_owned()),
        }
    }
    fn print_access(employee: &Employee) -> Result<(), String> {
        let access = try_access(employee)?;
        println!("Access granted!");
        Ok(())
    }
    let manager = Employee {
        position: Position::Manager,
        status: Status::Active,
    };
    print_access(&manager);
}
```

## Macro:
- Macro khác với function, macro có thể tạo ra code mới (trong quá trình biên dịch), còn function chỉ có thể thực hiện một hành động
- ### Declarative macro
    - Declarative macro là macro kiểu cơ bản nhất, hoạt động như **pattern matching** cho code

    ```rust
    macro_rules! say_hello { // khai báo macro
        () => { // macro này không nhận tham số
            println!("Hello, Rust 🦀"); // thực hiện hành động
        };
    }
    fn main() {
        say_hello!(); // gọi macro
    }
    ```

    - Declarative macro có thể nhận tham số và thực hiện các thao tác linh hoạt

    ```rust
    macro_rules! repeat { // khai báo macro
        ($text:expr, $n:expr) => { // macro nhận 2 tham số
            for _ in 0..$n {
                println!("{}", $text); // thực hiện hành động
            } 
        };
    }
    fn main() {
        repeat!("Hello", 3); // gọi macro
    }
    ```

- ### Procedural macro
    - Procedural macro là kiểu macro phức tạp hơn declarative macro, hoạt động như một **function** lấy input đầu vào là một Tokenstream, và trả về một Tokenstream khác

    - Dưới đây là ví dụ cơ bản
        - file `lib.rs`:

        ```rust
        use proc_macro::TokenStream;
        use quote::quote;
        use syn::{parse_macro_input, DeriveInput};

        #[proc_macro_derive(Getters)]
        pub fn getters_derive(input: TokenStream) -> TokenStream {
            let input = parse_macro_input!(input as DeriveInput); // chuyển đầu vào thành cấu trúc DeriveInput
            let struct_name = &input.ident;
            let fields = if let syn::Data::Struct(data) = input.data { // lấy các trường của struct đầu vào
                data.fields
            } else {
                panic!("Getters chỉ áp dụng cho struct!");
            };
            let getters = fields.iter().map(|field| { // tạo các hàm getter cho từng trường
                let field_name = &field.ident;
                let field_type = &field.ty;
                quote! {
                    impl #struct_name {
                        pub fn #field_name(&self) -> &#field_type { // hàm getter cho trườg
                            &self.#field_name // trả về giá trị của trường
                        }
                    }
                }
            });
            TokenStream::from(quote! { #(#getters)* }) // trả về code được tạo
        }
        ```

        - file `main.rs`:
          
        ```rust
        use macro_test::Getters; // sử dụng macro Getters

        fn main() {
            #[derive(Getters)] // sử dụng macro Getters
            struct User {
                name: String,
                age: u8,
                role: Role,
            }
            #[derive(Debug)]
            enum Role {
                Admin,
                Member,
            }
            let user = User {
                name: "MemeCoder".to_string(),
                age: 14,
                role: Role::Admin,
            };
            println!("{} ({} tuổi)", user.name(), user.age());
            match user.role {
                Role::Admin => println!("{} là quản trị viên", user.name()),
                Role::Member => println!("{} là thành viên", user.name()),
            }
        }
        ```

- ### Built-in macros
    - Trong rust có nhiều macro được **tích hợp sẵn**, ví dụ:

        | **Macro**                        | **Chức năng**          |  
        |----------------------------------|------------------------|  
        | `println!`                       | In ra màn hình         |  
        | `format!`                        | Tạo chuỗi string       |  
        | `vec!`                           | Tạo vector             |  
        | `assert!` và các `biến thể` khác   | Kiểm tra điều kiện     |

        ```rust
        fn main() {
            let v = vec![1, 2, 3]; // tạo vector 
            println!("Vec: {:?}", v); // in ra màn hình
            let s = format!("Sum: {}", 1 + 2); // tạo chuỗi
            println!("{}", s);
            assert!(2 + 2 == 4); // kiểm tra điều kiện
        }
        ```
