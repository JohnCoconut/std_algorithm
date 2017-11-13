## Function signatures

***************

```c++
template <class InputIt, class UnaryPredicate>
bool all_of(InputIt first, InputIt last, UnaryPredicate p);  (1)
template <class InputIt, class UnaryPredicate>
bool any_of(InputIt first, InputIt last, UnaryPredicate p);  (2)
template <class InputIt, class UnaryPredicate>
bool none_of(InputIt first, InputIt last, UnaryPredicate p); (3)
```
**************

All of them are non-modifying sequence operations. 
(1) `all_of` returns `true` if `[first, last)`  is empty or if `p(*i)` is `true` for every iterator i in range `[first, last)`, and `false` otherwise.

(2) `any_of` return `false` if `[first, last)` is empty of if `p(*i)` is `false` for every iterator i in range `[first, last)`, and `true` otherwise.

(3) `none_of` returns `true` if `[first, last)`  is empty or if `p(*i)` is `false` for every iterator i in range `[first, last)`, and `true` otherwise.

**[notes]** `all_of` is not the opposite of `none_of`. If the range `[first, last)` is empty, both of them return `true`.


### **Example Code**

#### Source code from `clang/lib/AST/VTableBuilder.cpp`

```c++
static void removeRedundantPaths(std::list<FullPathTy> &FullPaths) {
  FullPaths.remove_if([&](const FullPathTy &SpecificPath) {
    for (const FullPathTy &OtherPath : FullPaths) {
      if (&SpecificPath == &OtherPath)
        continue;
      if (std::all_of(SpecificPath.begin(), SpecificPath.end(),
                      [&](const BaseSubobject &BSO) {
                        return OtherPath.count(BSO) != 0;
                      })) {
        return true;
      }    
    }    
    return false;
  });  
}
```

* `removeRedundantPaths` function takes a single paramenter `std::list<FullPathTy>`. `std::list` has a member function `remove_if`, whose signature is,
```c++
template <class UnaryPredicate>
void remove_if(UnaryPredicate p);
```
 It removes all elements for which predicate `p` returns `true`.

* Unary predicate here is a lambda function, in the form of
```c++
[&](const FullPathTy &specification) { statements; }
```
 [&] captures all automatic variables used in the body of lambda by reference.

* `remove_if` function iterate over every element in the list. For each element, it will compare with other elements in the list.
```c++
if (&SpecificPath == &OtherPath)
```
 check whether `SpecificPath` and `OtherPath` refer to the same element.

* `all_of` returns `true` if every `BaseSubojbect` element in `SpecificPath` is also in `OtherPath`, which also means `SpecificPath` is a subset of `OtherPath`.

* In summary, `removeRedundantPaths` remove Paths that are subset of other Paths.


#### Source Code from `IncludeOS/src/net/dhcp/dhcpd.cpp`

```c++
const auto cid = get_client_id(msg);
if (cid.empty() or std::all_of(cid.begin(), cid.end(), [] (int i) { return i == 0;  })) {
debug("Invalid client id\n");
  return;
}
```

If `cid` is empty or `0.0.0.0`, it's invalid.
