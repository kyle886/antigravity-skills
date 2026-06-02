---
name: tdd-workflows-tdd-green
description: Implement the minimal code needed to make failing tests pass in the TDD green phase.
---

# Green Phase: Simple function
def product_list(request):
    products = Product.objects.all()
    return JsonResponse({'products': list(products.values())})

# Refactor: Class-based view
class ProductListView(View):
    def get(self, request):
        products = Product.objects.all()
        return JsonResponse({'products': list(products.values())})

# Refactor: Generic view
class ProductListView(ListView):
    model = Product
    context_object_name = 'products'
```

### Express Patterns

**Inline → Middleware → Service Layer:**
```javascript
// Green Phase: Inline logic
app.post('/api/users', (req, res) => {
  const user = { id: Date.now(), ...req.body };
  users.push(user);
  res.json(user);
});

// Refactor: Extract middleware
app.post('/api/users', validateUser, (req, res) => {
  const user = userService.create(req.body);
  res.json(user);
});

// Refactor: Full layering
app.post('/api/users',
  validateUser,
  asyncHandler(userController.create)
);
```

## Use this skill when

- Moving from red to green in a TDD cycle
- Implementing minimal behavior to satisfy tests
- You want to keep implementation intentionally simple

## Do not use this skill when

- You are refactoring for design or performance
- Tests are already passing and you need new requirements
- You need a full architectural redesign

## Instructions

1. Review failing tests and identify the smallest fix.
2. Implement the minimal change to pass the next test.
3. Run tests after each change to confirm progress.
4. Record shortcuts or debt for the refactor phase.
- Load the nested playbook at `resources/playbook.md` on-demand for detailed implementation guides and checklists.

## Safety

- Avoid bypassing tests to make them pass.
- Keep changes scoped to the failing behavior only.

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [tdd-workflows-tdd-red](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/tdd-workflows-tdd-red/SKILL.md) - Generate failing tests for the TDD red phase to define expected behavior and edge cases.
- [javascript-testing-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/javascript-testing-patterns/SKILL.md) - Implement comprehensive testing strategies using Jest, Vitest, and Testing Library for unit tests, i...
- [tdd-workflows-tdd-cycle](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/tdd-workflows-tdd-cycle/SKILL.md) - Use when working with tdd workflows tdd cycle

