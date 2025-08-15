# Aleo Types

## Primative

### Field
These are unsigned integers less than the modulus of the base field.
254-bit prime field element.

```
let a: field = 0field;
let b: field = 8444461749428370424248824938781546531375899335154063827935233455917409239040field;
```

### Unsigned integers
u8, u16, u32, u64

```
let b: u8 = 1u8;
let n: u64 = 1_000_000u64;
```

### Signed integers
i8, i16, i32, i64
```
let b: bool = false;
```

### Boolean
true or false
```
let b: bool = false;
```

### Addresses

## Composite

### Array

An array is a local data type, meaning it's used during local compilation and local execution, and are not stored on the blockchain. An array can have more than one element, but each element must be of the same type, and each array must have a pre-defined length. 2D arrays are possible.

```
    let matrix: [[u32; 3]; 2] = [
         [1u32, 2u32, 3u32],
         [4u32, 5u32, 6u32]
    ];
```
In the above example, we have a nested array of 2 rows. Each row contains 3 elements. Each element is of type u32. You can access an element in the array as follows: `arrayMatrix[row_index][column_index]`, or `let first_row_second_col: u32 = matrix[0u32][1u32];`

### Constant
A constant is a simple, immutable variable. They can be declaired globally or within a local function scope. Constants mus be known at compile time, as opposed to runtime.

```
    const FOO: u8 = 1u8;
```

### Record
Records represent user-owned data, like assets, credentials, or other private user data. They must contain an `owner`, which is an aleo address that controls the record. Records are consumed, and thus one-time use unless denoted otherwise. When a record passed as input ot a funciton it gets consumed/destroyed. To preserve the value, a new record(s) must be created as output(s) to the consuming function. 

#### Record Visibility
When passing a record as input to a program function, the `_nonce: group` is required to be set. Records have a default visibility of `private`, but can also be set to `constant` or `public`. 
- private: only owner can see the contents
- public: content visible to the blockchain
- constant: public structure with private values so others can verify properties about the record without seeing the actual data; selective disclosure and selective propery proofs

```
record Token {
    // The token owner
    owner: address,
    // The token amount
    amount: u64,
    // Required nonce for privacy
    nonce: group,
}
```

### Struct
A custom, static data type that groups related data together under one name, similar to Rust. However, all fields are public by default unless made private. All fields are present on every instance of the struct.

```
struct User {
    id: field;
    balance: u64;
    is_active: bool;
}

struct SecretUser {
    id: field.private;
    balance: u64.private;
    is_active: bool.private;
}
```

### Privacy
When you make something .private:

1. The verifier doesn’t know the value, only that it satisfies all constraints in your program.

2. The proof guarantees correctness without revealing the data.

3. This is why .private fields increase proof size and proving time — they require more constraints in the zk circuit.

```
record Token {
    owner: address.private;
    amount: u64;
}

const secret_value: u64.private = 42u64;

function secret_sum(a: u64.private, b: u64.private) -> u64.private {
    return a + b;
}
```

