# studlib

<div align="left">
  <img src="https://img.shields.io/badge/std-c99-black?logo=c&logoColor=fff" />
  <img src="https://img.shields.io/badge/license-MIT-orange?logo=github&logoColor=fff" />
</div>

Simple "STD" lib for C. <br />
Basically all functions return -1 if fail and 0 if success.

### Navigation
- [Collections](#collections)
  - [Linked List](#linked-list)
  - [Vector](#vector)
  - [Hashmap](#hashmap)
  - [Stack](#stack)

## Collections

### Linked list

Header file for LinkedList:

```C
#ifndef LINKEDLIST_H

#define LINKEDLIST_H

typedef struct node_t {
  void *value;
  struct node_t *next;
} node_t;

typedef struct {
  unsigned size;
  size_t value_size;
  node_t* next;
} linkedlist_t;

// Deinit popped node
int deinit_node(node_t **node);

// Make linked list
linkedlist_t *init_linkedlist(size_t value_size);

// Destroy list and its contents
int deinit_linkedlist(linkedlist_t **const list);

// Append value to the end
int append_linkedlist(linkedlist_t *const list, void *const value);

// Insert value in the beginning
int push_linkedlist(linkedlist_t *const list, void *const value);

// Pop the first node from list 
node_t *pop_linkedlist(linkedlist_t *const list);

// Remove last node in the list
int remove_last_linkedlist(linkedlist_t *const list);

#endif
```

#### Example usage

```C
int main(void) {
  ...
  *n1 = 24;
  *n2 = 17;

  linkedlist_t *list = init_linkedlist(sizeof(int));
  if (!list) {
    puts("Failed to initialize linked list.");
    return -1;
  }

  if (push_linkedlist(list, n1) != 0) {
    puts("Failed to push into the list.");
    return -1;
  }
  push_linkedlist(list, n2);

  /* first node */
  node_t *node = pop_linkedlist(list);
  if (!node) {
    puts("Failed to pop.");
    return -1;
  }

  printf("%d\n", *(int *)node->value); // 17
  deinit_node(&node);

  node_t *node2 = pop_linkedlist(list);
  deinit_node(&node2);

  /* popping node that doesn't exist */
  node_t *node3 = pop_linkedlist(list);
  int result = deinit_node(&node3);
  printf("%d\n", result); // -1

  deinit_linkedlist(&list);
    return 0;
}
```

### Vector

Initial vector capacity is `sizeof(void *)` ×16. <br/>
Capacity grows exponentially. <br/>
Vector header file:

```C
#ifndef VEC_H

#define VEC_H

typedef struct {
  size_t size;
  size_t capacity;
  void **arr;
} vec_t;

vec_t *init_vec(void);

vec_t *init_reserve_vec(size_t capacity);

int deinit_vec(vec_t **const vec);

// push in the end of vec
int push_vec(vec_t *vec, void *const value);

// macro that allocates value on heap, stores ptr in the vector
#define push_nvec(vec_ptr, result_ptr, value, type)

// removes last element and returns it
void* pop_vec(vec_t *const vec);

// removes element by index (shifts other elements BigO(n))
void* remove_vec(vec_t *const vec, size_t index);

// reverses the order of elements in vec
int reverse_vec(const vec_t *const vec);

#endif
```

#### Example usage

```C
int main(void) {
    ...
    yourtype_t *ur_type = malloc(sizeof(yourtype_t));
    *ur_type = ...;

    vec_t *vec = init_vec();
    if (!vec) {
        puts("Failed to initialize vector.");
        return -1;
    }

    int result = push_vec(vec, n0);
    if (result != 0) {
    puts("Failed to push into vec.");
    return result;
  }

  int res = 0;
  push_nvec(vec, &res, 1, int);
  push_nvec(vec, NULL, 2, int);
  push_nvec(vec, NULL, 'g', char);

  int res2 = push_vec(vec, ur_type);

  int reverse_result = reverse_vec(vec);
  if  (reverse_result != 0) {
    puts("Failed to reverse vector.");
    return reverse_result;
  }

  char *my_char = (char *)remove_vec(vec, 2);
  if (my_char) {
    /* ... */
    free(my_char);
  }

  deinit_vec(&vec);
  return 0;
}
```

### Hashmap

Header file for Hashmap:

```C
#ifndef HASHMAP_H

#define HASHMAP_H

typedef struct hashmap_node {
  void *key;
  void *value;
  struct hashmap_node *next;
} hashmap_node_t;

typedef struct {
  hashmap_node_t **buckets;
  size_t size;
  // pointers to functions
  unsigned (*hash_func)(void *key);
  int (*key_cmp_func)(void *key1, void *key2);
} hashmap_t;

// Default functions
// Use these or implement your own

#ifndef HASHMAP_FUNCS

#define HASHMAP_FUNCS

int compare_strings(void *key1, void *key2);

unsigned int hash_string(void *key);

#endif

// END

hashmap_t *init_hashmap(const size_t capacity,
                        unsigned int (*hash_func)(void *key),
                        int (*key_cmp_func)(void *key1, void *key2));

int deinit_hashmap(hashmap_t **const map);

int push_hashmap(hashmap_t *const map, void *const key, void *const value);

void *get_hashmap(hashmap_t *const map, void *const key);

int remove_hashmap(hashmap_t *const map, void *const key);

#endif
```

### Stack

Initial stack capacity is `sizeof(void *)` ×16. <br/>
Capacity grows exponentially. <br/>
Header file for Stack:

```C
#ifndef STACK_H

#define STACK_H

typedef struct {
  size_t capacity;
  size_t size;
  // array of pointer to the values
  void **values;
} mstack_t;

mstack_t* init_stack(void);

int deinit_stack(mstack_t **stack);

// macro that allocates value on heap, stores ptr in the stack
#define push_nstack(stack_ptr, result_ptr, value, type)

// pushes an item in front of entire array (stack->values)
int push_stack(mstack_t *const stack, void *const value);

// get the first item in stack
// NOTE: don't forget to free the returned value!
void* pop_stack(mstack_t *const stack);

#endif
```

#### Example usage

```C
int main(void) {
  mytype_t *my_type = malloc(sizeof(mytype_t));
  *my_type = ...;

  mstack_t *stack = init_stack();
  if (!stack) {
    puts("Failed to initialize stack.");
    return -1;
  }

  if (push_stack(stack, my_type) != 0) {
    puts("Failed to push into stack.");
    deinit_stack(&stack);
    return -1;
  }

  int result = 0;
  push_nstack(stack, &result, 24, int);
  if (result != 0) {
    puts("Failed to push into stack.");
    deinit_stack(&stack);
    return -1;
  }

  push_nstack(stack, NULL, 17, int);

  int *popped = (int *)pop_stack(stack);
  if (!popped) {
    puts("Failed to pop.");
    deinit_stack(&stack);
    return -1;
  }
  printf("value: %d\n", *popped);
  free(popped);

  int *popped2 = (int *)pop_stack(stack);
  printf("value: %d\n", *popped);
  free(popped2);

  int *popped3 = (int *)pop_stack(stack);
  if (!popped3) {
    puts("nothing was to pop");
    deinit_stack(&stack);
    return -1;
  }

  deinit_stack(&stack);
  return 0;
}
```

## Todo

- Heap `src/collections/heap`
  - `heap_t *init_heap(void)`
  - `int deinit_heap(heap_t **heap)`
  - ...
- Queue `src/collections/queue`
  - `queue_t *init_queue(void)`
  - `int deinit_queue(queue_t **queue)`
  - ...
- Tree `src/collections/tree`
  - `tree_t *init_tree(void)`
  - `int deinit_tree(tree_t **tree)`
  - ...

## PS

If anyone uses this library, please report any bugs or memory leaks!

<div align="left">
  <a href="https://github.com/Makefolder/studlib/issues/new">
    <b>Report a bug</b>
  <a/>
<div/>


