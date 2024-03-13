## Двоични файлове
- лесни за четене от машина, трудни за четене от човек
- за четене и писане в двоични файлове използваме функциите:

    `write (const char* s, streamsize n);` 
    - `s` е указател към мястото в паметта, от където взимаме байтовете, които записваме във файла
    - `n` указва броя байтове, които записваме във файла <br>

    `read (char* s, streamsize n);`
   - `s` е указател към мястото в паметта, където записваме байтовете, които сме прочели от файла
    - `n` указва броя байтове, които четем от файла <br> 
- функциите приемат `char*`, затова, ако искаме да запазим друг тип данни, трябва експлицитно да ги преобразуваме към `char*` (едни и същи байтове се интеропретират по различен начин)

```cpp
#include<fstream>

int main() {
    std::ofstream outFile("file.txt", std::ios::binary);

    int x = 26;

    //трябва ни адреса на x, затова използваме &x
    outFile.write((const char*)&x, sizeof(x));

    outFile.close();
}
```

```cpp
#include<iostream>
#include<fstream>

int main() {
    std::ifstream inFile("file.txt", std::ios::binary);

    int x;
    inFile.read((char*)&x, sizeof(x)); //x = 26

    inFile.close();
}
```

- байтовете се записват в обратна посока - старшият бит е накрая (little endian)

## Сереализация на структури
- ако в структурата няма динамична памет, можем да запишем целия блок от байтове наведнъж

```cpp
#include <iostream>
#include <fstream>

struct Test {
	bool b;
	int x;
};

int main()
{
	{
		Test t = { true, 45 };
		std::ofstream file("testObj.dat", std::ios::binary);

		if (!file.is_open()) {
			return -1;
		}

		file.write((const char*)&t, sizeof(t));
	}

	{
		Test t;
		std::ifstream file("testObj.dat", std::ios::binary);

		if (!file.is_open()) {
			return -1;
		}

		file.read((char*)&t, sizeof(t));
		std::cout << t.b << " " << t.x << std::endl;
	}
}
```

```cpp
#include <iostream>
#include <fstream>
#include <cstring>
#pragma warning (disable: 4996)

struct Student {
	char name[25];
	int fn;
	int age;
};

int main()
{
	{
		Student st;
		strcpy(st.name, "Ivan");
		st.fn = 1234;
		st.age = 33;

		std::ofstream file("student.dat", std::ios::binary);

		if (!file.is_open()) {
			std::cout << "Error while writing to binary file!" << std::endl;
			return -1;
		}

		//since the data is grouped in the struct, we can save it like this.
		file.write((const char*)&st, sizeof(st));
	}

	{
		Student st;
		std::ifstream file("student.dat", std::ios::binary);

		if (!file.is_open()) {
			std::cout << "Error while reading from binary file!" << std::endl;
			return -1;
		}

		file.read((char*)&st, sizeof(st));
		std::cout << st.name << " " << st.fn << " " << st.age << std::endl;
	}
}
```

## Задачи

**Задача 1.** 

Да се реализира funkciq, която при подаден двоичен файл има масив от числа, записва в 2 различни двоични файла четните и нечетните числа от него.