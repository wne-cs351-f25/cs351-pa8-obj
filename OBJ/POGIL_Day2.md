# PA8 POGIL Activity - Day 2: Polymorphism and Advanced Scoping

## CS351 Programming Languages - OBJ Language Deep Dive

**Instructions**: Continue working in your groups from Day 1. Today we explore the more advanced features of OBJ that make it unique among object-oriented languages.

---

Group Member 1:
Group Member 2:
Group Member 3:
Group Member 4:

---

### Running Code Examples

Use the same workflow from Day 1:

```bash
# Option 1: Direct REPL
rep
# Then type/paste code

# Option 2: File + Interactive (Recommended)
(cat pogil/i.obj; cat) | rep
```

---

## Part 5: Combined Inheritance Patterns (20 minutes)

_Based on Lessons 9-11_

### Model 5A: Static and Object Inheritance Together

Run `pogil/i.obj`:

```obj
% Program I
define Company = class
    static companyName = "TechCorp"
    static employeeCount = 0

    field employeeId
    field salary

    method init = proc(sal) {
        set <Company>employeeCount = add1(<Company>employeeCount);
        set employeeId = <Company>employeeCount;
        set salary = sal;
        this
    }

    method getId = proc() employeeId
    method getSalary = proc() salary
    method getCompanyName = proc() <self>companyName
end

define Manager = class extends Company
    static companyName = "TechCorp Management"  % Override static
    field teamSize

    method init = proc(sal, team) {
        .<super>init(sal);
        set teamSize = team;
        this
    }

    method getTeamSize = proc() teamSize
end

define emp1 = .<new Company>init(50000)
define mgr1 = .<new Manager>init(80000, 5)
define emp2 = .<new Company>init(55000)

.<emp1>getId()           % 1
.<mgr1>getId()           % 2
.<emp2>getId()           % 3
.<emp1>getCompanyName()  % "TechCorp"
.<mgr1>getCompanyName()  % "TechCorp Management"
.<mgr1>getTeamSize()     % 5

% What's the total count?
<Company>employeeCount   % 3
<Manager>employeeCount   % 3 (same!)
```

**Critical Thinking Questions:**

1. What is `<Company>employeeCount` after creating all three employees?

2. What is `<Manager>employeeCount`? Why is it the same as Company's count?

3. Why does `.<mgr1>getCompanyName()` return `"TechCorp Management"` instead of `"TechCorp"`? What would happen if we used `<myclass>` instead of `<self>`?

### Model 5B: Advanced Shadowing

Run `pogil/j.obj`:

```obj
% Program J
define Base = class
    static x = 100
    field x  % Same name!

    method init = proc() {
        set x = 200;
        this
    }

    method getStaticX = proc() <myclass>x
    method getInstanceX = proc() x
end

define Derived = class extends Base
    static x = 300  % Shadow static
    field x  % Shadow instance

    method init = proc() {
        .<super>init();
        set x = 400;
        this
    }
end

define obj = .<new Derived>init()
.<obj>getStaticX()    % 100 (Base's static, because <myclass> is bound to Base)
.<obj>getInstanceX()  % 200 (Base's instance field)

% Direct access tests:
<Base>x        % 100
<Derived>x     % 300
<obj>x         % 400 (Derived's instance field!)
```

**Critical Thinking Questions:**

4. Why does `.<obj>getStaticX()` return 100 instead of 300?

5. Why does `.<obj>getInstanceX()` return 200 instead of 400?

6. When you access `<obj>x` directly, you get 400. What's the difference between direct access and method access?

**Synthesis Question:**

7. This shadowing behavior is unlike Java/C++. In Java, you can't shadow fields like this. What does this teach you about how OBJ handles field lookups in methods vs direct access?

---

## Part 6: Polymorphism and Dynamic Dispatch (25 minutes)

_Based on Lesson 12_

### Model 6A: Understanding `self` vs `this`

Run `pogil/k.obj`:

```obj
% Program K
define Shape = class
    field color

    method init = proc(c) {
        set color = c;
        this
    }

    method describeWithSelf = proc() {
        % Using self for dynamic dispatch
        .<self>getType()
    }

    method describeWithThis = proc() {
        % Using this for static dispatch
        .<this>getType()
    }

    method getType = proc() 1  % Returns 1 for shape
end

define Circle = class extends Shape
    field radius

    method init = proc(c, r) {
        .<super>init(c);
        set radius = r;
        this
    }

    method getType = proc() 2  % Returns 2 for circle
end

define Square = class extends Shape
    field side

    method init = proc(c, s) {
        .<super>init(c);
        set side = s;
        this
    }

    method getType = proc() 3  % Returns 3 for square
end

define c = .<new Circle>init("red", 5)
define s = .<new Square>init("blue", 10)

% Test with self (dynamic dispatch):
.<c>describeWithSelf()  % Returns 2 (Circle's getType)
.<s>describeWithSelf()  % Returns 3 (Square's getType)

% Test with this (static dispatch):
.<c>describeWithThis()  % Returns 1 (Shape's getType)
.<s>describeWithThis()  % Returns 1 (Shape's getType)
```

**Critical Thinking Questions:**

8. What's the difference in output between using `.<self>getType()` and `.<this>getType()`?

9. When Shape's `describeWithSelf` method calls `.<self>getType()`, which version of `getType` runs?

10. Why might you want to use `<this>` instead of `<self>` sometimes? (Hint: think about intentionally calling the base class version)

### Model 6B: External vs Internal Dispatch (Template Method Pattern)

Run `pogil/l.obj`:

```obj
% Program L
define Logger = class
    method log = proc(msg) {
        .<self>getPrefix()  % Call subclass's getPrefix, return the code
    }

    method getPrefix = proc() 1  % 1 = LOG
end

define TimestampLogger = class extends Logger
    method getPrefix = proc() 2  % 2 = TIMESTAMP
end

define ErrorLogger = class extends Logger
    method getPrefix = proc() 3  % 3 = ERROR

    method logError = proc(msg) {
        .<super>log(msg)  % Call parent's log
    }
end

define tLogger = new TimestampLogger
define eLogger = new ErrorLogger

.<tLogger>log("test")      % Returns 2 (TIMESTAMP prefix)
.<eLogger>log("test")      % Returns 3 (ERROR prefix)
.<eLogger>logError("test") % Returns 3 (self still refers to ErrorLogger)
```

**Critical Thinking Questions:**

11. What prefix code is returned when `TimestampLogger` logs a message?

12. When `ErrorLogger`'s `logError` calls `.<super>log(msg)`, which `getPrefix` gets called? How does `<self>` work even though we called through `<super>`?

13. How would the behavior change if Logger's `log` used `.<this>getPrefix()` instead?

**Synthesis Question:**

14. This demonstrates the Template Method pattern. Can you think of a real-world example where you'd want this behavior? (Hint: consider a game engine with customizable rendering)

---

## Part 7: Environment Navigation Symbols (20 minutes)

_Based on Lessons 13-15_

### Model 7A: The Power of `<!@>` (Lexical Environment)

Run `pogil/m.obj`:

```obj
% Program M - Lexical Environment Access
define outerVar = 999

define ClassWithClosure = class
    field instanceVar

    method init = proc(val) {
        set instanceVar = val;
        this
    }

    method accessLexical = proc()
        <!@>outerVar  % Access lexical environment variable

    method accessInstance = proc()
        instanceVar
end

define obj = .<new ClassWithClosure>init(111)
.<obj>accessLexical()    % 999
.<obj>accessInstance()   % 111

% Can we change outerVar?
set outerVar = 777
.<obj>accessLexical()  % 777 now!
```

**Critical Thinking Questions:**

15. What value does `<!@>outerVar` access? Where is this variable defined?

16. After changing `outerVar` to 777, what does `accessLexical` return? What does this tell you about `<!@>`?

17. What's the difference between accessing `<!@>outerVar` vs just `outerVar` from inside a method?

### Model 7B: Current Environment with `@`

Run `pogil/n.obj`:

```obj
% Program N - Current Environment Access
define ContextClass = class
    method getEnv = proc()
        @  % Just @ by itself - returns current environment
end

define obj = new ContextClass
.<obj>getEnv()  % Returns environment object
```

**Critical Thinking Questions:**

18. What does `@` represent? What type of value does it return?

19. How is `@` different from `<this>`? (Hint: `this` is an object, `@` is an environment)

### Model 7C: Practical Symbol Usage

Run `pogil/o.obj`:

```obj
% Program O - Using myclass and superclass
define ConfigurableClass = class
    static defaultSize = 10
    field size
    field data

    method init = proc() {
        set size = <self>defaultSize;  % Use self for runtime class
        set data = "initial";
        this
    }

    method resetToParentDefault = proc() {
        set size = <superclass>defaultSize;
        this
    }

    method cloneFrom = proc(other) {
        set size = <other>size;
        set data = <other>data;
        this
    }
end

define CustomConfig = class extends ConfigurableClass
    static defaultSize = 20

    method init = proc() {
        .<super>init();  % Call parent init
        this
    }

    method getMyDefault = proc() <self>defaultSize
    method getParentDefault = proc() <superclass>defaultSize
end

define obj1 = .<new CustomConfig>init()
<obj1>size                 % 20 (CustomConfig's defaultSize)
.<obj1>getMyDefault()      % 20
.<obj1>getParentDefault()  % 10
```

**Critical Thinking Questions:**

20. What's the initial size of `obj1`? Why does it use CustomConfig's `defaultSize` even though the `init` method is defined in ConfigurableClass?

21. When would you use `<superclass>` instead of naming the parent class directly? What advantage does this provide?

**Synthesis Question:**

22. OBJ has 6 special symbols: `self`, `this`, `super`, `myclass`, `superclass`, `<!@>`. Create a one-sentence description for when to use each.

---

## Integration Challenge: Understanding Binding (15 minutes)

### Challenge: Predict the Output

Work through this example as a group and predict the output before running it:

```obj
define A = class
    static s = 100
    field f

    method init = proc() {
        set f = 200;
        this
    }

    method testMyclass = proc() <myclass>s
    method testSelf = proc() <self>s
    method testThis = proc() <this>f
end

define B = class extends A
    static s = 300
    field f

    method init = proc() {
        .<super>init();
        set f = 400;
        this
    }

    method testSuper = proc() <super>f
end

define obj = .<new B>init()

% Predict these outputs BEFORE running:
.<obj>testMyclass()  % ?
.<obj>testSelf()     % ?
.<obj>testThis()     % ?
.<obj>testSuper()    % ?
<obj>f               % ?
```

**Group Tasks:**

23. Predict each output and explain your reasoning

24. Run the code and verify your predictions

25. For any that you got wrong, explain why the actual output differs from your prediction

26. Draw an environment diagram showing the object structure and how each symbol resolves

---

## Summary Cheat Sheet

| Symbol       | Binding Type     | Resolves To                     | Use Case                                        |
| ------------ | ---------------- | ------------------------------- | ----------------------------------------------- |
| `self`       | Dynamic (deep)   | Runtime class of base object    | Polymorphic calls, accessing overridden statics |
| `this`       | Static (shallow) | Current object part             | Access current class's members explicitly       |
| `super`      | Static (shallow) | Parent object part              | Call overridden parent methods                  |
| `myclass`    | Static           | Class where code is written     | Access static members (non-polymorphic)         |
| `superclass` | Static           | Parent class                    | Access parent's static members                  |
| `<!@>`       | Lexical          | Environment where class defined | Access closure variables                        |
| `@`          | Dynamic          | Current environment             | Reflection/debugging                            |

---

_Course content developed by Declan Gray-Mullen for WNEU with Claude_
