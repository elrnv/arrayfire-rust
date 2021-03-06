% Indexing

[Indexer](./struct.Indexer.html) structure is the key element used in Rust
wrapper for ArrayFire for creating references to existing Arrays. Given
below are few of such functions and their corresponding example use cases.
Use [Indexer::new](./struct.Indexer.html#method.new) to create an Indexer
object and set either a `Seq` object or `Array` as indexing object for a
given dimension.

# Using Seq for all dimensions

Create a view of an existing Array using Sequences and [index](./fn.index.html).

```rust
let dims = Dim4::new(&[5, 5, 1, 1]);
let a = randu::<f32>(dims);
af_print!("a", a);
//a
//[5 5 1 1]
//    0.3990     0.5160     0.8831     0.9107     0.6688
//    0.6720     0.3932     0.0621     0.9159     0.8434
//    0.5339     0.2706     0.7089     0.0231     0.1328
//    0.1386     0.9455     0.9434     0.2330     0.2657
//    0.7353     0.1587     0.1227     0.2220     0.2299

// Index array using sequences
let seqs = &[Seq::new(1u32, 3, 1), Seq::default()];
let sub = index(&a, seqs);
af_print!("a(seq(1,3,1), span)", sub);
// [3 5 1 1]
//     0.6720     0.3932     0.0621     0.9159     0.8434
//     0.5339     0.2706     0.7089     0.0231     0.1328
//     0.1386     0.9455     0.9434     0.2330     0.2657
```

Set a sub-portion of an existing Array with a constant value using [assign_seq](./fn.assign_seq.html).

```rust
let a    = constant(2.0 as f32, Dim4::new(&[5, 3, 1, 1]));
let b    = constant(1.0 as f32, Dim4::new(&[3, 3, 1, 1]));
let seqs = &[Seq::new(1.0, 3.0, 1.0), Seq::default()];
let sub  = assign_seq(&a, seqs, &b);
print(&a);
// 2.0 2.0 2.0
// 2.0 2.0 2.0
// 2.0 2.0 2.0
// 2.0 2.0 2.0
// 2.0 2.0 2.0

print(&sub);
// 2.0 2.0 2.0
// 1.0 1.0 1.0
// 1.0 1.0 1.0
// 1.0 1.0 1.0
// 2.0 2.0 2.0
```

# Using Array and Seq combination

Create a view of an existing Array using another Array and Sequence.

```rust
use arrayfire::{Array, Dim4, Seq, print, randu, index_gen, Indexer};
let values: [f32; 3] = [1.0, 2.0, 3.0];
let indices = Array::new(&values, Dim4::new(&[3, 1, 1, 1]));
let seq4gen = Seq::new(0.0, 2.0, 1.0);
let a = randu::<f32>(Dim4::new(&[5, 3, 1, 1]));
// [5 3 1 1]
//     0.0000     0.2190     0.3835
//     0.1315     0.0470     0.5194
//     0.7556     0.6789     0.8310
//     0.4587     0.6793     0.0346
//     0.5328     0.9347     0.0535

let mut idxrs = Indexer::new();
idxrs.set_index(&indices, 0, None); // 2nd parameter is indexing dimension
idxrs.set_index(&seq4gen, 1, Some(false)); // 3rd parameter indicates batch operation

let sub2 = index_gen(&a, idxrs);
println!("a(indices, seq(0, 2, 1))"); print(&sub2);
// [3 3 1 1]
//     0.1315     0.0470     0.5194
//     0.7556     0.6789     0.8310
//     0.4587     0.6793     0.0346
```

Set a sub-portion of an existing Array with another Array using a combination
of `Seq` and `Array`.

 ```rust
 use arrayfire::{Array, Dim4, Seq, print, randu, constant, Indexer, assign_gen};
 let values: [f32; 3] = [1.0, 2.0, 3.0];
 let indices = Array::new(&values, Dim4::new(&[3, 1, 1, 1]));
 let seq4gen = Seq::new(0.0, 2.0, 1.0);
 let a = randu::<f32>(Dim4::new(&[5, 3, 1, 1]));
 // [5 3 1 1]
 //     0.0000     0.2190     0.3835
 //     0.1315     0.0470     0.5194
 //     0.7556     0.6789     0.8310
 //     0.4587     0.6793     0.0346
 //     0.5328     0.9347     0.0535

 let b    = constant(2.0 as f32, Dim4::new(&[3, 3, 1, 1]));

 let mut idxrs = Indexer::new();
 idxrs.set_index(&indices, 0, None); // 2nd parameter is indexing dimension
 idxrs.set_index(&seq4gen, 1, Some(false)); // 3rd parameter indicates batch operation

 let sub2 = assign_gen(&a, &idxrs, &b);
 println!("a(indices, seq(0, 2, 1))"); print(&sub2);
 // [5 3 1 1]
 //     0.0000     0.2190     0.3835
 //     2.0000     2.0000     2.0000
 //     2.0000     2.0000     2.0000
 //     2.0000     2.0000     2.0000
 //     0.5328     0.9347     0.0535
 ```

# Extract or Set rows/coloumns

Extract a specific set of rows/coloumns from an existing Array.

```rust
let dims = Dim4::new(&[5, 5, 1, 1]);
let a = randu::<f32>(dims);
println!("Grab last row of the random matrix");
print(&a);
print(&row(&a, 4));
print(&col(&a, 4));
```

You can also use [rows](./fn.rows.html) & [cols](./fn.cols.html) to retrieve a
subset of rows or coloumns respectively.

Similarly, [set_row](./fn.set_row.html) & [set_rows](./fn.set_rows.html) can be used to change the values in a particular set of rows using another Array. [set_col](./fn.set_col.html) & [set_cols](./fn.set_cols.html) has same functionality, except that it is for coloumns.
