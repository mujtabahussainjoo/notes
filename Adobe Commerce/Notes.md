
## Dependency Injection in PHP

### 1) Using `new B()` inside `A`

```php
class B
{
    public function work()
    {
        return "done";
    }
}

class A
{
    public function doSomething()
    {
        $b = new B();
        return $b->work();
    }
}

$a = new A();
echo $a->doSomething();
```

**What happens here:**
- `A` creates `B` by itself.
- `A` is responsible for both:
  - making `B`
  - using `B`
- So `A` is tightly connected to `B`.

---

### 2) DI: `B` is given to `A`

```php
class B
{
    public function work()
    {
        return "done";
    }
}

class A
{
    private B $b;

    public function __construct(B $b)
    {
        $this->b = $b;
    }

    public function doSomething()
    {
        return $this->b->work();
    }
}

$b = new B();
$a = new A($b);
echo $a->doSomething();
```

**What happens here:**
- `B` is still created using `new B()`
- but not inside `A`
- `A` just receives it and uses it
- So `A` is responsible only for using `B`, not creating `B`.

---

### 3) The Actual Coding Difference

#### With `new`
```php
class A
{
    public function doSomething()
    {
        $b = new B();
        return $b->work();
    }
}
```

#### With DI
```php
class A
{
    public function __construct(B $b)
    {
        $this->b = $b;
    }
}
```

**Difference:**
- First one: creation happens inside the method
- Second one: creation happens outside and object is passed in

---

### 4) Why This Matters in Code

If later you want `A` to use another `B`, with `new` you must edit `A`.

#### Hardcoded `new`
```php
$b = new B();
```

If tomorrow you want:
```php
$b = new B2();
```
you must change the code inside `A`.

#### With DI
```php
public function __construct(B $b)
```

Now `A` does not care who creates `B`. You can create `B`, `B2`, or a test version outside and pass it in.

---

### 5) Shortest Way to See the Difference

#### `new`
```php
$a = new A();
```
Inside `A`, it does:
```php
$b = new B();
```

#### DI
```php
$b = new B();
$a = new A($b);
```

**So:**
- `new` = class makes its own dependency
- DI = dependency is made before and passed in

---

### 6) Very Simple Conclusion

In both cases, yes, `B` is created.

But the important thing is:
- with `new`, `A` creates `B`
- with DI, `A` does not create `B`, it only uses it

**That is why DI is different.** It separates object creation from object usage.

---

## Factory Pattern vs `new` in Magento

### 1) Using `new` Operator

```php
class B
{
    public function work()
    {
        return "done";
    }
}

class A
{
    public function doSomething()
    {
        $b = new B();
        return $b->work();
    }
}
```

**What happens here:**
- `A` directly creates `B`.
- `A` is responsible for both:
  - creating `B`
  - using `B`

**Problem:**
- `A` is tightly coupled to `B`.
- If later you want another class instead of `B`, you must edit `A`.

---

### 2) Using a Factory Class

```php
class B
{
    public function work()
    {
        return "done";
    }
}

class BFactory
{
    public function create(): B
    {
        return new B();
    }
}

class A
{
    private BFactory $bFactory;

    public function __construct(BFactory $bFactory)
    {
        $this->bFactory = $bFactory;
    }

    public function doSomething()
    {
        $b = $this->bFactory->create();
        return $b->work();
    }
}
```

**What happens here:**
- `A` does **not** create `B` directly.
- `A` asks `BFactory` to create `B`.
- Factory handles object creation.

---

### 3) Difference in Simple Words

#### With `new`
`A` says:
> "I will create `B` myself."

#### With factory
`A` says:
> "Factory, please create `B` for me."

So in both cases `B` is created, but:
- with `new`, `A` creates it directly
- with factory, another class creates it

---

### 4) Why Factory is Better Than Direct `new`

#### With `new`
```php
$b = new B();
```
- hardcoded
- less flexible
- harder to change later

#### With factory
```php
$b = $this->bFactory->create();
```
- creation is separated
- `A` stays cleaner
- easier to manage object creation

---

### 5) Why Magento Uses Factories

Magento uses factories when:
- you need to create objects manually at runtime
- you want multiple instances
- you do not want to hardcode `new` everywhere

**So:**
- **DI** = object is injected into class
- **Factory** = class asks another class to create object
- **`new`** = class creates object itself

---

### 6) Short Summary

- `new B()` means `A` creates `B`.
- `BFactory->create()` means factory creates `B`.
- DI means `B` is passed into `A` from outside.

---

## Magento DI Example

Here is a very simple Magento-style example using only constructor DI.

### Class B

```php
class B
{
    public function work()
    {
        return "done";
    }
}
```

### Class A

```php
class A
{
    private B $b;

    public function __construct(B $b)
    {
        $this->b = $b;
    }

    public function doSomething()
    {
        return $this->b->work();
    }
}
```

### How Magento Uses This

Magento sees that `A` needs `B` in its constructor, so it creates `B` and gives it to `A` when `A` is built.

**So the flow is:**
- `A` says: "I need `B`."
- Magento creates `B`.
- Magento passes `B` into `A`.

---

### Relating it to `new`

#### With `new`
```php
class A
{
    public function doSomething()
    {
        $b = new B();
        return $b->work();
    }
}
```
Here `A` creates `B` by itself.

#### With DI
```php
class A
{
    public function __construct(B $b)
    {
        $this->b = $b;
    }

    public function doSomething()
    {
        return $this->b->work();
    }
}
```
Here `A` does not create `B`. It only uses `B`.

---

### Why This is Useful

- `A` stays focused on its own job.
- Magento controls object creation.
- The code is easier to manage and extend.

### One-line Summary

`new B()` means `A` creates `B`, while `__construct(B $b)` means `A` receives `B` from outside.

---

## Practical Example: Product Creation

### Using `new`

```php
class Product
{
    public function __construct($name = 'Phone')
    {
        $this->name = $name;
    }
}

class Order
{
    public function place()
    {
        $product = new Product();
        return $product->name;
    }
}
```

### Suppose Later Creation Logic Changes

Now you want every product to be created with a different name, like `"Laptop"` instead of `"Phone"`.

With `new`, you must change `Order`:

```php
class Order
{
    public function place()
    {
        $product = new Product('Laptop');
        return $product->name;
    }
}
```

**Why this is a problem:**
`Order` had to change just because the way `Product` is created changed. That means `Order` is doing more than its own job.

---

### With Factory

```php
class Product
{
    public function __construct($name = 'Phone')
    {
        $this->name = $name;
    }
}

class ProductFactory
{
    public function create()
    {
        return new Product('Phone');
    }
}

class Order
{
    private ProductFactory $productFactory;

    public function __construct(ProductFactory $productFactory)
    {
        $this->productFactory = $productFactory;
    }

    public function place()
    {
        $product = $this->productFactory->create();
        return $product->name;
    }
}
```

### Later Change

If you want `"Laptop"` now, you only change the factory:

```php
class ProductFactory
{
    public function create()
    {
        return new Product('Laptop');
    }
}
```

`Order` stays the same.

---

### Simple Takeaway

- With `new`, creation logic is inside `Order`, so `Order` must change.
- With factory, creation logic is inside the factory, so only the factory changes.

**That is why factory is easier to maintain.**

---

### When to Use Factory

Use factory when:
- you need to create objects at runtime
- you may create many instances
- you want to keep creation logic outside your main class
- the object may need setup in one place

For example, in Magento, factories are commonly used for models or objects that should not be created directly inside business code.

---

### Advantages of Factory Over `new`

- Better code organization.
- Less tight coupling.
- Easier to change creation logic later.
- Easier to test.
- Cleaner main class code.

---

### Easy Comparison

#### `new`
```php
$order = new Order();
$product = new Product();
```
`Order` or your code creates the object directly.

#### Factory
```php
$product = $productFactory->create();
```
A factory creates the object for you.

---

### Simple Magento Relation

In Magento, factories are often used when you need a new object instance without writing `new` inside your class.

**So the idea is:**
- **DI** = receive dependencies
- **Factory** = create objects when needed
- **`new`** = create object directly inside code

---

### Very Short Conclusion

Use `new` for simple, direct, one-time object creation.

Use factory when you want cleaner Magento-style code and want to separate object creation from object use.

---
