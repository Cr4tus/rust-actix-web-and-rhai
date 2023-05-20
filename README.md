# Simple Web Project

## What does the app do ?
The web server accepts GET request like this one: http://addr/multiply/first/second and will respond with the result of the multiplication or addition (these 2 operations have been implemented so far).

## Programming Languages:
- Rust
- rhai

## Frameworks & Libraries:
- Actix Web - https://actix.rs
- rhai - https://rhai.rs, https://github.com/rhaiscript

## Implementation:
There is only one rust file (main.rs) and 2 **.rhai** files
Once the server receives a valid request, the parameters are being registered, as the multiplication and addition have been implemented in separate files using **rhai** like this:
```rhai
fn multiply(first, second) {
    return first * second;
}

let first = first();
let second = second();

multiply(first, second);
```

```rhai
fn add(first, second) {
    return first + second;
}

let first = first();
let second = second();

add(first, second);
```
The registration of the 2 parameters looks like this:
```rust
#[actix::get("/multiply/{first}/{second}")]
async fn multiply(path: actix::web::Path<(i64, i64)>) -> impl actix::Responder {
    let (first, second) = path.into_inner();

    let mut engine = rhai::Engine::new();

    engine.register_fn("first", move || first);
    engine.register_fn("second", move || second);

    let result = engine.eval_file::<i64>("src/multiply.rhai".into()).unwrap();

    format!("{result}")
}
```
The code sample from above shows the definition for the endpoint where you can submit 2 numbers and get the result of their multiplication.
