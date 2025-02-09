# Transformation de classe C++ en code ODIN
<br>

Synthèse des explications de Rickard Andersson (https://www.youtube.com/watch?v=bJUnCXEIie0)
<br><br>

## Code C++
Interprèteur C++ (https://www.onlinegdb.com/online_c++_debugger)  

```
#include <cstdint>
#include <cstdio>
//#include <memory>
#include <vector>

class Element {
public:
	int64_t value;

	Element() {
		//printf("--Element constructor null value: %p\n", this);
		value = 0;
	}

	Element(int64_t v) {
		//printf("--Element constructor with value: %p\n", this);
		value = v;
	}

	~Element() {
		//printf("--Element destructor: %p\n", this);
		value = 0;
	}
};

// RAII: Ressource Acquisition Is Initialization
class Container {
public:
	Container() {
		printf("Container constructor (null, init to 2 elements): %p\n", this);

		m_value = 0;
		//m_vector = std::vector<Element>();
		m_vector.push_back(1);
		m_vector.push_back(2);
	}

	~Container() {
		printf("Container destruction: %p\n", this);

		m_value = 0;
	}

	Container(int64_t value) {
		printf("Container constructor with value (with 3 elements): %p\n", this);

		m_value = value;
		//m_vector = std::vector<Element>();
		m_vector.push_back(1);
		m_vector.push_back(2);
		m_vector.push_back(3);
	}

	Container(const Container &b) {
		printf("Container copy constructor (+4th element): %p\n", this);

		m_value = b.m_value;
		m_vector = std::vector<Element>(b.m_vector);
		m_vector.push_back(4);
	}

	Container(Container &&b) {
		printf("Container move constructor: %p\n", this);

		m_value = b.m_value;
		m_vector = std::move(b.m_vector);
	}

	Container &operator=(const Container &b) {
		printf("Container copy assigment: %p\n", this);

		m_value = b.m_value;
		m_vector = std::vector<Element>(b.m_vector);

		return *this;
	}

	Container &operator=(Container &&b) {
		printf("Container move assigment: %p\n", this);

		m_value = b.m_value;
		m_vector = std::move(b.m_vector);

		return *this;
	}

	int64_t value() const {
		return m_value;
	}

	void printVector() const {
		printf("%p = ", m_vector.data());
		for (auto it = m_vector.begin(); it != m_vector.end(); ++it) {
			if (it != m_vector.begin()) {
				printf(", ");
			}
			printf("%ld", it->value);
		}
		printf("\n");
	}

private:
	int64_t m_value;
	std::vector<Element> m_vector;
};


int main() {
	printf("-- Scope 0 Début --\n");
	Container e0;
	printf("e0.m_value: %ld\n", e0.value());
	printf("e0.vector: ");
	e0.printVector();

	{
		printf("\n-- Scope 1 Début --\n");
		Container e1{1};
		printf("e1.m_value: %ld\n", e1.value());
		printf("e1.vector: ");
		e1.printVector();
		printf("-- Fin Scope 1 --\n");
	}

	{
		printf("\n-- Scope 2 Début --\n");
		Container e2{42};
		Container e3(e2);
		printf("e2.m_value: %ld\ne3.m_value: %ld\n", e2.value(), e3.value());
		printf("e2.vector: ");
		e2.printVector();
		printf("e3.vector: ");
		e3.printVector();
		printf("-- Fin Scope 2 --\n");
	}

	printf("-- Fin Scope 0 --\n");
}
```
Résultat:  
```
-- Scope 0 Début --
Container constructor (null, init to 2 elements): 0x7ffdb800eba0
--Element constructor with value: 0x7ffdb800eb70
--Element destructor: 0x7ffdb800eb70
--Element constructor with value: 0x7ffdb800eb70
--Element destructor: 0x5d8722be66c0
--Element destructor: 0x7ffdb800eb70
e0.m_value: 0
e0.vector: 0x5d8722be66e0 = 1, 2

-- Scope 1 Début --
Container constructor with value (with 3 elements): 0x7ffdb800ebe0
--Element constructor with value: 0x7ffdb800eb70
--Element destructor: 0x7ffdb800eb70
--Element constructor with value: 0x7ffdb800eb70
--Element destructor: 0x5d8722be66c0
--Element destructor: 0x7ffdb800eb70
--Element constructor with value: 0x7ffdb800eb70
--Element destructor: 0x5d8722be6700
--Element destructor: 0x5d8722be6708
--Element destructor: 0x7ffdb800eb70
e1.m_value: 1
e1.vector: 0x5d8722be6720 = 1, 2, 3
-- Fin Scope 1 --
Container destruction: 0x7ffdb800ebe0
--Element destructor: 0x5d8722be6720
--Element destructor: 0x5d8722be6728
--Element destructor: 0x5d8722be6730

-- Scope 2 Début --
Container constructor with value (with 3 elements): 0x7ffdb800ebc0
--Element constructor with value: 0x7ffdb800eb70
--Element destructor: 0x7ffdb800eb70
--Element constructor with value: 0x7ffdb800eb70
--Element destructor: 0x5d8722be6700
--Element destructor: 0x7ffdb800eb70
--Element constructor with value: 0x7ffdb800eb70
--Element destructor: 0x5d8722be66c0
--Element destructor: 0x5d8722be66c8
--Element destructor: 0x7ffdb800eb70
Container copy constructor (+4th element): 0x7ffdb800ebe0
--Element constructor with value: 0x7ffdb800eb60
--Element destructor: 0x5d8722be66c0
--Element destructor: 0x5d8722be66c8
--Element destructor: 0x5d8722be66d0
--Element destructor: 0x7ffdb800eb60
e2.m_value: 42
e3.m_value: 42
e2.vector: 0x5d8722be6720 = 1, 2, 3
e3.vector: 0x5d8722be6750 = 1, 2, 3, 4
-- Fin Scope 2 --
Container destruction: 0x7ffdb800ebe0
--Element destructor: 0x5d8722be6750
--Element destructor: 0x5d8722be6758
--Element destructor: 0x5d8722be6760
--Element destructor: 0x5d8722be6768
Container destruction: 0x7ffdb800ebc0
--Element destructor: 0x5d8722be6720
--Element destructor: 0x5d8722be6728
--Element destructor: 0x5d8722be6730
-- Fin Scope 0 --
Container destruction: 0x7ffdb800eba0
--Element destructor: 0x5d8722be66e0
--Element destructor: 0x5d8722be66e8
```

<br><br><br>

## Code Odin
Utilisation de struct et de tableay dynamique avec création des méthodes de libération de la mémoire.  
```
package cpp_exemple
import "core:fmt"

Element :: struct {
	value: int,
}

element_delete :: proc(element: Element) {
	fmt.printfln("--Element_delete: %d", element.value)
}


Container :: struct {
	value:   int,
	vectors: [dynamic]Element,
}

container_make :: proc(value: int, allocator := context.allocator) -> Container {
	fmt.printfln("Container_make with value (with 3 elements): %d", value)

	values := make([dynamic]Element, 0, 3, allocator)
	append(&values, Element{1}, Element{2}, Element{3})

	return Container{value = value, vectors = values}
}

container_delete :: proc(container: Container) {
	fmt.printfln("Container_delete: %d", container.value)

	for e in container.vectors {
		element_delete(e)
	}
	delete(container.vectors)
}

container_copy :: proc(container: Container, allocator := context.allocator) -> Container {
	fmt.printfln("Container_copy + 4th element: %d", container.value)

	vectors := make([dynamic]Element, 0, len(container.vectors) + 1, allocator)
	append(&vectors, ..container.vectors[:])
	append(&vectors, Element{4})

	return Container{value = container.value, vectors = vectors}
}

container_print_vector :: proc(container: ^Container) {
	fmt.printf("%p = ", &container.vectors)
	for e, i in container.vectors {
		if i != 0 {
			fmt.printf(", ")
		}
		fmt.printf("%d", e.value)
	}
	fmt.println()
}

main :: proc() {
	fmt.printfln("-- Scope 0 Début --")
	e0: Container
	defer container_delete(e0)
	fmt.printfln("e0.value: %d", e0.value)
	fmt.printf("e0.vector: ")
	container_print_vector(&e0)

	{
		fmt.printfln("-- Scope 1 Début --")
		e1 := container_make(1)
		defer container_delete(e1)
		fmt.printfln("e1.value: %d", e1.value)
		fmt.printf("e1.vector: ")
		container_print_vector(&e1)
		fmt.printfln("-- Fin Scope 1 --")
	}

	{
		fmt.printfln("-- Scope 2 Début --")
		e2 := container_make(2)
		defer container_delete(e2)
		e3 := container_copy(e2)
		defer container_delete(e3)

		fmt.printfln("e2.value: %d", e2.value)
		fmt.printf("e2.vector: ")
		container_print_vector(&e2)

		fmt.printfln("e3.value: %d", e3.value)
		fmt.printf("e3.vector: ")
		container_print_vector(&e3)

		fmt.printfln("update e3 value to 3")
		e3.value = 3

		fmt.printfln("-- Fin Scope 2 --")
	}

	fmt.printfln("-- Fin Scope 0 --")
}

```
Résultat:  
```
-- Scope 0 Début --
e0.value: 0
e0.vector: 0x7FFFFFFFD418 = 
-- Scope 1 Début --
Container_make with value (with 3 elements): 1
e1.value: 1
e1.vector: 0x7FFFFFFFD388 = 1, 2, 3
-- Fin Scope 1 --
Container_delete: 1
--Element_delete: 1
-- Scope 0 Début --
e0.value: 0
e0.vector: 0x7FFFFFFFD418 = 
-- Scope 1 Début --
Container_make with value (with 3 elements): 1
e1.value: 1
e1.vector: 0x7FFFFFFFD388 = 1, 2, 3
-- Fin Scope 1 --
Container_delete: 1
--Element_delete: 1
--Element_delete: 2
--Element_delete: 3
-- Scope 2 Début --
Container_make with value (with 3 elements): 2
Container_copy + 4th element: 2
e2.value: 2
e2.vector: 0x7FFFFFFFD318 = 1, 2, 3
e3.value: 2
e3.vector: 0x7FFFFFFFD2B8 = 1, 2, 3, 4
-- Fin Scope 2 --
Container_delete: 2
--Element_delete: 1
--Element_delete: 2
--Element_delete: 3
--Element_delete: 4
-- Scope 0 Début --
e0.value: 0
e0.vector: 0x7FFFFFFFD418 = 
-- Scope 1 Début --
Container_make with value (with 3 elements): 1
e1.value: 1
e1.vector: 0x7FFFFFFFD388 = 1, 2, 3
-- Fin Scope 1 --
Container_delete: 1
--Element_delete: 1
--Element_delete: 2
--Element_delete: 3
-- Scope 2 Début --
Container_make with value (with 3 elements): 2
Container_copy + 4th element: 2
e2.value: 2
e2.vector: 0x7FFFFFFFD318 = 1, 2, 3
e3.value: 2
e3.vector: 0x7FFFFFFFD2B8 = 1, 2, 3, 4
update e3 value to 3
-- Fin Scope 2 --
Container_delete: 3
--Element_delete: 1
--Element_delete: 2
--Element_delete: 3
--Element_delete: 4
-- Scope 0 Début --
e0.value: 0
e0.vector: 0x7FFFFFFFD418 = 
-- Scope 1 Début --
Container_make with value (with 3 elements): 1
e1.value: 1
e1.vector: 0x7FFFFFFFD388 = 1, 2, 3
-- Fin Scope 1 --
Container_delete: 1
--Element_delete: 1
--Element_delete: 2
--Element_delete: 3
-- Scope 2 Début --
Container_make with value (with 3 elements): 2
Container_copy + 4th element: 2
e2.value: 2
e2.vector: 0x7FFFFFFFD318 = 1, 2, 3
e3.value: 2
e3.vector: 0x7FFFFFFFD2B8 = 1, 2, 3, 4
update e3 value to 3
-- Fin Scope 2 --
Container_delete: 3
--Element_delete: 1
--Element_delete: 2
--Element_delete: 3
--Element_delete: 4
Container_delete: 2
--Element_delete: 1
--Element_delete: 2
--Element_delete: 3
-- Fin Scope 0 --
Container_delete: 0
```
