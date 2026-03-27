## Задание № 1. Наследование.

У нас уже есть класс преподавателей и класс студентов. Преподаватели бывают разные, поэтому теперь класс Mentor должен стать родительским классом, а от него нужно реализовать наследование классов Lecturer (лекторы) и Reviewer (эксперты, проверяющие домашние задания). Очевидно, имя, фамилия и список закрепленных курсов логично реализовать на уровне родительского класса.

### Решение:

```

class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}

class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []
        
class Lecturer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
               
class Reviewer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
      
    def rate_hw(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            if course in student.grades:
                student.grades[course] += [grade]
            else:
                student.grades[course] = [grade]
        else:
            return 'Ошибка'
        
        
lecturer = Lecturer('Иван', 'Иванов')
reviewer = Reviewer('Пётр', 'Петров')
print(isinstance(lecturer, Mentor)) # True
print(isinstance(reviewer, Mentor)) # True
print(lecturer.courses_attached)    # []
print(reviewer.courses_attached)    # []

```

## Задание № 2. Атрибуты и взаимодействие классов.

Реализуйте метод выставления оценок лекторам у класса Student (оценки по 10-балльной шкале, хранятся в атрибуте-словаре у Lecturer, в котором ключи – названия курсов, а значения – списки оценок). Лектор при этом должен быть закреплен за тем курсом, на который записан студент.

### Решение:

```

class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}
    
    # Метод для выставления оценки лектору
    def rate_lecture(self, lecturer, course, grade):
        if isinstance(lecturer, Lecturer) and course in lecturer.courses_attached and course in self.courses_in_progress:
            if course in lecturer.grades:
                lecturer.grades[course].append(grade)
            else:
                lecturer.grades[course] = [grade]
        else:
            return 'Ошибка'

class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []

class Lecturer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
        self.grades = {}  # Словарь для хранения оценок от студентов

class Reviewer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
    
    def rate_hw(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            if course in student.grades:
                student.grades[course].append(grade)
            else:
                student.grades[course] = [grade]
        else:
            return 'Ошибка'


lecturer = Lecturer('Иван', 'Иванов')
reviewer = Reviewer('Пётр', 'Петров')
student = Student('Алёхина', 'Ольга', 'Ж')
 
student.courses_in_progress += ['Python', 'Java']
lecturer.courses_attached += ['Python', 'C++']
reviewer.courses_attached += ['Python', 'C++']
 
print(student.rate_lecture(lecturer, 'Python', 7))   # None
print(student.rate_lecture(lecturer, 'Java', 8))     # Ошибка
print(student.rate_lecture(lecturer, 'С++', 8))      # Ошибка
print(student.rate_lecture(reviewer, 'Python', 6))   # Ошибка
 
print(lecturer.grades)  # {'Python': [7]}  

```

## Задание № 3. Полиморфизм и магические методы

1. Перегрузите магический метод __str__ у всех классов.

У проверяющих он должен выводить информацию в следующем виде:

```
print(some_reviewer)
Имя: Some
Фамилия: Buddy

```

У лекторов:

```
print(some_lecturer)
Имя: Some
Фамилия: Buddy
Средняя оценка за лекции: 9.9

```

А у студентов так:

```

print(some_student)
Имя: Ruoy
Фамилия: Eman
Средняя оценка за домашние задания: 9.9
Курсы в процессе изучения: Python, Git
Завершенные курсы: Введение в программирование

```
2. Реализуйте возможность сравнивать (через операторы сравнения) между собой лекторов по средней оценке за лекции и студентов по средней оценке за домашние задания.

### Решение:

```

class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}

    def rate_lecture(self, lecturer, course, grade):
        if isinstance(lecturer, Lecturer) and course in lecturer.courses_attached and course in self.courses_in_progress:
            if course in lecturer.grades:
                lecturer.grades[course].append(grade)
            else:
                lecturer.grades[course] = [grade]
        else:
            return 'Ошибка'

    def avg_grade(self):
        all_grades = [grade for grades in self.grades.values() for grade in grades]
        return sum(all_grades) / len(all_grades) if all_grades else 0

    def __str__(self):
        courses_in_progress = ', '.join(self.courses_in_progress)
        finished_courses = ', '.join(self.finished_courses)
        avg = round(self.avg_grade(), 1)
        return (f"Имя: {self.name}\n"
                f"Фамилия: {self.surname}\n"
                f"Средняя оценка за домашние задания: {avg}\n"
                f"Курсы в процессе изучения: {courses_in_progress}\n"
                f"Завершенные курсы: {finished_courses}")

    # Методы сравнения по средней оценке
    def __lt__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.avg_grade() < other.avg_grade()

    def __le__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.avg_grade() <= other.avg_grade()

    def __eq__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.avg_grade() == other.avg_grade()

    def __ne__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.avg_grade() != other.avg_grade()

    def __gt__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.avg_grade() > other.avg_grade()

    def __ge__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.avg_grade() >= other.avg_grade()


class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []


class Lecturer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
        self.grades = {}

    def avg_grade(self):
        all_grades = [grade for grades in self.grades.values() for grade in grades]
        return sum(all_grades) / len(all_grades) if all_grades else 0

    def __str__(self):
        avg = round(self.avg_grade(), 1)
        return (f"Имя: {self.name}\n"
                f"Фамилия: {self.surname}\n"
                f"Средняя оценка за лекции: {avg}")

    # Методы сравнения по средней оценке
    def __lt__(self, other):
        if not isinstance(other, Lecturer):
            return NotImplemented
        return self.avg_grade() < other.avg_grade()

    def __le__(self, other):
        if not isinstance(other, Lecturer):
            return NotImplemented
        return self.avg_grade() <= other.avg_grade()

    def __eq__(self, other):
        if not isinstance(other, Lecturer):
            return NotImplemented
        return self.avg_grade() == other.avg_grade()

    def __ne__(self, other):
        if not isinstance(other, Lecturer):
            return NotImplemented
        return self.avg_grade() != other.avg_grade()

    def __gt__(self, other):
        if not isinstance(other, Lecturer):
            return NotImplemented
        return self.avg_grade() > other.avg_grade()

    def __ge__(self, other):
        if not isinstance(other, Lecturer):
            return NotImplemented
        return self.avg_grade() >= other.avg_grade()


class Reviewer(Mentor):
    def rate_hw(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            if course in student.grades:
                student.grades[course].append(grade)
            else:
                student.grades[course] = [grade]
        else:
            return 'Ошибка'
    
    def __str__(self):
        return (f"Имя: {self.name}\n"
                f"Фамилия: {self.surname}")
        

student1 = Student('Ruoy', 'Eman', 'M')
student1.courses_in_progress += ['Python', 'Git']
student1.finished_courses += ['Введение в программирование']
student1.grades = {'Python': [10, 9], 'Git': [8]}

lecturer1 = Lecturer('Some', 'Buddy')
lecturer1.grades = {'Python': [10, 9], 'Git': [9]}

reviewer1 = Reviewer('Peter', 'Petrov')
reviewer1.courses_attached += ['Python', 'Git']

print(student1)
# Имя: Ruoy
# Фамилия: Eman
# Средняя оценка за домашние задания: 9.0
# Курсы в процессе изучения: Python, Git
# Завершенные курсы: Введение в программирование

print(lecturer1)
# Имя: Some
# Фамилия: Buddy
# Средняя оценка за лекции: 9.3

print(reviewer1)
# Имя: Peter
# Фамилия: Petrov

# Сравнение студентов и лекторов по средней оценке:
student2 = Student('Anna', 'Smith', 'F')
student2.grades = {'Python': [9, 8]}
lecturer2 = Lecturer('John', 'Doe')
lecturer2.grades = {'Python': [8]}

print(student1 > student2)  # True (9.0 > 8.5)
print(lecturer1 < lecturer2)  # False (9.3 > 8.0)

```

## Задание № 4. Полевые испытания

Создайте по 2 экземпляра каждого класса, вызовите все созданные методы, а также реализуйте две функции:

1. для подсчета средней оценки за домашние задания по всем студентам в рамках конкретного курса (в качестве аргументов принимаем список студентов и название курса);

2. для подсчета средней оценки за лекции всех лекторов в рамках курса (в качестве аргумента принимаем список лекторов и название курса).
   
### Решение:

```

class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}  # Оценки студента

    def rate_lecture(self, lecturer, course, grade):
        """Оценка лектора студентом"""
        if isinstance(lecturer, Lecturer) and course in lecturer.courses_attached and course in self.courses_in_progress:
            lecturer.grades.setdefault(course, []).append(grade)
        else:
            return 'Ошибка'

    def avg_grade(self):
        """Средняя оценка студента"""
        all_grades = [grade for grades in self.grades.values() for grade in grades]
        return sum(all_grades) / len(all_grades) if all_grades else 0

    def __str__(self):
        """Строковое представление студента"""
        return (
            f"Имя: {self.name}\n"
            f"Фамилия: {self.surname}\n"
            f"Средняя оценка за домашние задания: {self.avg_grade():.1f}\n"
            f"Курсы в процессе изучения: {', '.join(self.courses_in_progress) or 'нет'}\n"
            f"Завершенные курсы: {', '.join(self.finished_courses) or 'нет'}"
        )

    # Методы сравнения студентов
    def __lt__(self, other):
        if not isinstance(other, Student): return NotImplemented
        return self.avg_grade() < other.avg_grade()
    def __le__(self, other):
        if not isinstance(other, Student): return NotImplemented
        return self.avg_grade() <= other.avg_grade()
    def __eq__(self, other):
        if not isinstance(other, other): return NotImplemented
        return self.avg_grade() == other.avg_grade()
    def __ne__(self, other):
        if not isinstance(other, Student): return NotImplemented
        return self.avg_grade() != other.avg_grade()
    def __gt__(self, other):
        if not isinstance(other, Student): return NotImplemented
        return self.avg_grade() > other.avg_grade()
    def __ge__(self, other):
        if not isinstance(other, Student): return NotImplemented
        return self.avg_grade() >= other.avg_grade()


# === Класс Mentor ===
class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []


# === Класс Lecturer ===
class Lecturer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
        self.grades = {}  # Оценки лектора

    def avg_grade(self):
        """Средняя оценка лектора"""
        all_grades = [grade for grades in self.grades.values() for grade in grades]
        return sum(all_grades) / len(all_grades) if all_grades else 0

    def __str__(self):
        """Строковое представление лектора"""
        return (
            f"Имя: {self.name}\n"
            f"Фамилия: {self.surname}\n"
            f"Средняя оценка за лекции: {self.avg_grade():.1f}"
        )

    # Методы сравнения лекторов
    def __lt__(self, other):
        if not isinstance(other, Lecturer): return NotImplemented
        return self.avg_grade() < other.avg_grade()
    def __le__(self, other):
        if not isinstance(other, Lecturer): return NotImplemented
        return self.avg_grade() <= other.avg_grade()
    def __eq__(self, other):
        if not isinstance(other, Lecturer): return NotImplemented
        return self.avg_grade() == other.avg_grade()
    def __ne__(self, other):
        if not isinstance(other, Lecturer): return NotImplemented
        return self.avg_grade() != other.avg_grade()
    def __gt__(self, other):
        if not isinstance(other, Lecturer): return NotImplemented
        return self.avg_grade() > other.avg_grade()
    def __ge__(self, other):
        if not isinstance(other, Lecturer): return NotImplemented
        return self.avg_grade() >= other.avg_grade()

class Reviewer(Mentor):
    def rate_hw(self, student, course, grade):
        """Оценка домашнего задания проверяющим"""
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            student.grades.setdefault(course, []).append(grade)
        else:
            return 'Ошибка'

    def __str__(self):
        """Строковое представление проверяющего"""
        return f"Имя: {self.name}\nФамилия: {self.surname}"


# === Глобальные функции для подсчета средних оценок ===

# Подсчет средней оценки студентов по курсу
def avg_hw_students(students, course_name):
    grades = []
    for student in students:
        grades.extend(student.grades.get(course_name, []))
    return sum(grades) / len(grades) if grades else 0

# Подсчет средней оценки лекторов по курсу
def avg_lecturers(lecturers, course_name):
    grades = []
    for lecturer in lecturers:
        grades.extend(lecturer.grades.get(course_name, []))
    return sum(grades) / len(grades) if grades else 0


# === Тестирование системы ===

# Создание экземпляров
student1 = Student('Иван', 'Иванов', 'Мужской')
student2 = Student('Мария', 'Петрова', 'Женский')
lecturer1 = Lecturer('Алексей', 'Смирнов')
lecturer2 = Lecturer('Ольга', 'Кузнецова')
reviewer1 = Reviewer('Дмитрий', 'Волков')
reviewer2 = Reviewer('Елена', 'Орлова')

# Курсы
student1.courses_in_progress += ['Python', 'Git']
student2.courses_in_progress += ['Python']
student1.finished_courses += ['Введение в программирование']

lecturer1.courses_attached += ['Python']
lecturer2.courses_attached += ['Python']

reviewer1.courses_attached += ['Python', 'Git']
reviewer2.courses_attached += ['Python']

# Оценки
reviewer1.rate_hw(student1, 'Python', 9)
reviewer1.rate_hw(student1, 'Python', 8)
reviewer1.rate_hw(student1, 'Git', 7)
reviewer2.rate_hw(student2, 'Python', 10)
reviewer2.rate_hw(student2, 'Python', 9)

student1.rate_lecture(lecturer1, 'Python', 9)
student2.rate_lecture(lecturer1, 'Python', 8)
student1.rate_lecture(lecturer2, 'Python', 10)
student2.rate_lecture(lecturer2, 'Python', 9)

# Печать информации
print("Студент 1:", "\n", student1, "\n")
print("Студент 2:", "\n", student2, "\n")
print("Лектор 1:", "\n", lecturer1, "\n")
print("Лектор 2:", "\n", lecturer2, "\n")
print("Проверяющий 1:", "\n", reviewer1, "\n")
print("Проверяющий 2:", "\n", reviewer2, "\n")

# Сравнения
print(f"Сравнение студентов: student1 > student2? {student1 > student2}", "\n")
print(f"Сравнение лекторов: lecturer1 < lecturer2? {lecturer1 < lecturer2}", "\n")

# Расчет средних оценок
students_list = [student1, student2]
lecturers_list = [lecturer1, lecturer2]
course = "Python"

avg_hw = avg_hw_students(students_list, course)
avg_lect = avg_lecturers(lecturers_list, course)

print(f"Средняя оценка за домашние задания по курсу '{course}': {avg_hw:.1f}", "\n")
print(f"Средняя оценка за лекции по курсу '{course}': {avg_lect:.1f}")


```