class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}

    def average_grade(self):
        total_grades = [grade for grades in self.grades.values() for grade in grades]
        return sum(total_grades) / len(total_grades) if total_grades else 0

    def __str__(self):
        avg_grade = self.average_grade()
        courses_in_progress = ', '.join(self.courses_in_progress)
        finished_courses = ', '.join(self.finished_courses)
        return (f"Имя: {self.name}\n"
                f"Фамилия: {self.surname}\n"
                f"Средняя оценка за домашние задания: {avg_grade:.1f}\n"
                f"Курсы в процессе изучения: {courses_in_progress}\n"
                f"Завершенные курсы: {finished_courses}")

    def rate_lecturer(self, lecturer, course, grade):
        if isinstance(lecturer, Lecturer) and course in self.courses_in_progress and course in lecturer.courses_attached:
            if course in lecturer.grades:
                lecturer.grades[course].append(grade)
            else:
                lecturer.grades[course] = [grade]
        else:
            return 'Ошибка'

    def __lt__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.average_grade() < other.average_grade()

class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []

    def __str__(self):
        return f"Имя: {self.name}\nФамилия: {self.surname}"

class Lecturer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
        self.grades = {}

    def average_grade(self):
        total_grades = [grade for grades in self.grades.values() for grade in grades]
        return sum(total_grades) / len(total_grades) if total_grades else 0

    def __str__(self):
        avg_grade = self.average_grade()
        return (f"Имя: {self.name}\n"
                f"Фамилия: {self.surname}\n"
                f"Средняя оценка за лекции: {avg_grade:.1f}")

    def __lt__(self, other):
        if not isinstance(other, Lecturer):
            return NotImplemented
        return self.average_grade() < other.average_grade()

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
        return f"Имя: {self.name}\nФамилия: {self.surname}"

# Создание объектов
student1 = Student('Иван', 'Иванов', 'мужчина')
student2 = Student('Анна', 'Петрова', 'женщина')

lecturer1 = Lecturer('Алексей', 'Смирнов')
lecturer2 = Lecturer('Мария', 'Васильева')

reviewer1 = Reviewer('Екатерина', 'Сидорова')
reviewer2 = Reviewer('Александр', 'Попов')

# Привязка курсов
student1.courses_in_progress.append('Python')
student2.courses_in_progress.append('Python')

lecturer1.courses_attached.append('Python')
lecturer2.courses_attached.append('Python')

reviewer1.courses_attached.append('Python')
reviewer2.courses_attached.append('Python')

# Оценки студентам за домашние задания
reviewer1.rate_hw(student1, 'Python', 8)
reviewer1.rate_hw(student1, 'Python', 9)
reviewer2.rate_hw(student2, 'Python', 10)
reviewer2.rate_hw(student2, 'Python', 9)

# Оценки лекторам за лекции
student1.rate_lecturer(lecturer1, 'Python', 10)
student1.rate_lecturer(lecturer1, 'Python', 9)
student2.rate_lecturer(lecturer2, 'Python', 8)
student2.rate_lecturer(lecturer2, 'Python', 9)

# Вывод информации
print(student1, end='\n\n')
print(student2, end='\n\n')
print(lecturer1, end='\n\n')
print(lecturer2, end='\n\n')
print(reviewer1, end='\n\n')
print(reviewer2, end='\n\n')

# Сравнение студентов и лекторов
print(student1 > student2, end='\n\n')  # Сравнивает студентов по средней оценке
print(lecturer1 < lecturer2, end='\n\n')  # Сравнивает лекторов по средней оценке

# Функции для подсчета средней оценки

def average_student_grade_for_course(students, course):
    total_grades = []
    for student in students:
        if course in student.grades:
            total_grades.extend(student.grades[course])
    if total_grades:
        return sum(total_grades) / len(total_grades)
    return 0

def average_lecturer_grade_for_course(lecturers, course):
    total_grades = []
    for lecturer in lecturers:
        if course in lecturer.grades:
            total_grades.extend(lecturer.grades[course])
    if total_grades:
        return sum(total_grades) / len(total_grades)
    return 0

# Подсчет средней оценки
students = [student1, student2]
lecturers = [lecturer1, lecturer2]

print(f"Средняя оценка за домашние задания по курсу Python: {average_student_grade_for_course(students, 'Python'):.1f}")
print(f"Средняя оценка за лекции по курсу Python: {average_lecturer_grade_for_course(lecturers, 'Python'):.1f}")