# Week 4 Comprehensive Assessment

Fork this repository, update this file to include your answers, and submit a pull request. You may refer to any notes, past projects, or external resources you want. The questions do not have to be completed in order.

1. My app has Students who are organized into Houses. What lines should I add to `config/routes.rb` to create a complete set of nested RESTful routes for these resources?

resources :houses do
  resources :students
end

2. Write a migration to create a `students` table with columns for name, age, and something to link the student to the `House` they belong to.

rails g migration CreateStudents name age:integer house references:houses
create_table :students do |t|
  t.text :name
  t.integer :age
  t.references :house, index: true
end

3. Assuming a Student can only be in a single House, what kind of associations should I define on these models? Do I need to introduce any new models, and if so, what associations should I define on those?

Student
  belongs_to :house
end

House
  has_many :students
end

4. Now I want Students to be able to enroll in Courses. What kind of associations should I define on these models? Do I need to introduce any new models, and if so, what associations should I define on those?

class Student
  has_many :student_courses
  has_many :courses, through :student_courses
end

class StudentCourse
  belongs_to :student
  belongs_to :course
end

class Course
  has_many :student_courses
  has_many :students, through: :student_courses
end

Student has_many :courses dependent: :destroy
(WRONG)

5. I want Students to be able to leave Comments on both Courses and Professors. What kind of associations should I define on these models? Do I need to introduce any new models, and if so, what associations should I define on those? (POLYMORPHIC)

class Comment
  belongs_to :student
  belongs_to :commentable, polymorphic: true
end

class Course
  has_many :comments, as:commentable
end

class Professor
  has_many :comments, as:commentable
end

class Student
  has_many :comments
end

Course has_many :comments, through: :students
Professor has_many :comments, through: :students
(WRONG)

6. When creating a new Student in my students controller, assuming I have a `student_params` method and the house the student should belong to is stored in `@house`, what code should I write to link the student to the house?

@student = @house.students.new(student_params)
@student = Student.new(student_params)
@student.house = @house

new_student = @house.create(name: 'Bob', age: 20)
(WRONG)

7. If I'm using Devise with a User model, what code should I write in a controller or view to find out whether a user is signed in? And how can I get a reference to the currently-signed-in user?

<% if user_signed_in? %>
<%= current_user.email %>

8. If I'm using Devise with a User model, what code should I write in my students controller so that only signed-in users can access the `new` and `create` actions?

class StudentsController
  before_action :authenticate_user!, only: [:new, :create]
end

validates :user, presence: true
(WRONG)

9. When I deploy a new version of my app to Heroku that contains a new migration, how can I run that migration on my production database?

$ heroku run rake db:migrate

10. If accessing my app on Heroku gives me an uninformative "something went wrong" error message, how can I find out what the real error is?

$ heroku logs
