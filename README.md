# Finals-project

# Create Django project
django-admin startproject grading_system

# Navigate into the project directory
cd grading_system

# Create a new Django app
python manage.py startapp core

# Create necessary files for Django Rest Framework
pip install djangorestframework

# Update settings.py


INSTALLED_APPS = [
    ...
    'rest_framework',
    'core',
]

AUTH_USER_MODEL = 'core.User'



from django.db import models
from django.contrib.auth.models import AbstractUser

class User(AbstractUser):
    is_student = models.BooleanField(default=False)
    is_teacher = models.BooleanField(default=False)

class Course(models.Model):
    name = models.CharField(max_length=255)
    description = models.TextField()
    teacher = models.ForeignKey(User, on_delete=models.CASCADE, related_name='courses')

class Enrollment(models.Model):
    student = models.ForeignKey(User, on_delete=models.CASCADE, related_name='enrollments')
    course = models.ForeignKey(Course, on_delete=models.CASCADE, related_name='enrollments')

class Assignment(models.Model):
    course = models.ForeignKey(Course, on_delete=models.CASCADE, related_name='assignments')
    title = models.CharField(max_length=255)
    description = models.TextField()
    due_date = models.DateTimeField()

class Submission(models.Model):
    assignment = models.ForeignKey(Assignment, on_delete=models.CASCADE, related_name='submissions')
    student = models.ForeignKey(User, on_delete=models.CASCADE, related_name='submissions')
    file = models.FileField(upload_to='submissions/')
    submission_date = models.DateTimeField(auto_now_add=True)

class Grade(models.Model):
    submission = models.ForeignKey(Submission, on_delete=models.CASCADE, related_name='grades')
    grade = models.IntegerField()
    feedback = models.TextField()


    from rest_framework import serializers
from .models import User, Course, Enrollment, Assignment, Submission, Grade

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = '__all__'

class CourseSerializer(serializers.ModelSerializer):
    class Meta:
        model = Course
        fields = '__all__'

class EnrollmentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Enrollment
        fields = '__all__'

class AssignmentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Assignment
        fields = '__all__'

class SubmissionSerializer(serializers.ModelSerializer):
    class Meta:
        model = Submission
        fields = '__all__'

class GradeSerializer(serializers.ModelSerializer):
    class Meta:
        model = Grade
        fields = '__all__'

        

from rest_framework import viewsets
from .models import User, Course, Enrollment, Assignment, Submission, Grade
from .serializers import UserSerializer, CourseSerializer, EnrollmentSerializer, AssignmentSerializer, SubmissionSerializer, GradeSerializer

class UserViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer

class CourseViewSet(viewsets.ModelViewSet):
    queryset = Course.objects.all()
    serializer_class = CourseSerializer

class EnrollmentViewSet(viewsets.ModelViewSet):
    queryset = Enrollment.objects.all()
    serializer_class = EnrollmentSerializer

class AssignmentViewSet(viewsets.ModelViewSet):
    queryset = Assignment.objects.all()
    serializer_class = AssignmentSerializer

class SubmissionViewSet(viewsets.ModelViewSet):
    queryset = Submission.objects.all()
    serializer_class = SubmissionSerializer

class GradeViewSet(viewsets.ModelViewSet):
    queryset = Grade.objects.all()
    serializer_class = GradeSerializer



    from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import UserViewSet, CourseViewSet, EnrollmentViewSet, AssignmentViewSet, SubmissionViewSet, GradeViewSet

router = DefaultRouter()
router.register(r'users', UserViewSet)
router.register(r'courses', CourseViewSet)
router.register(r'enrollments', EnrollmentViewSet)
router.register(r'assignments', AssignmentViewSet)
router.register(r'submissions', SubmissionViewSet)
router.register(r'grades', GradeViewSet)

urlpatterns = [
    path('', include(router.urls)),
]



from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('core.urls')),
]



npx create-react-app frontend
cd frontend
npm install axios react-router-dom



import React from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import CourseList from './components/CourseList';
import CourseDetail from './components/CourseDetail';
import AssignmentList from './components/AssignmentList';
import AssignmentDetail from './components/AssignmentDetail';
import Gradebook from './components/Gradebook';

function App() {
  return (
    <Router>
      <Switch>
        <Route path="/courses" component={CourseList} />
        <Route path="/course/:id" component={CourseDetail} />
        <Route path="/assignments" component={AssignmentList} />
        <Route path="/assignment/:id" component={AssignmentDetail} />
        <Route path="/gradebook" component={Gradebook} />
      </Switch>
    </Router>
  );
}

export default App;



import React, { useEffect, useState } from 'react';
import axios from 'axios';
import { Link } from 'react-router-dom';

function CourseList() {
  const [courses, setCourses] = useState([]);

  useEffect(() => {
    axios.get('/api/courses/')
      .then(response => {
        setCourses(response.data);
      })
      .catch(error => {
        console.error("There was an error fetching the courses!", error);
      });
  }, []);

  return (
    <div>
      <h1>Courses</h1>
      <ul>
        {courses.map(course => (
          <li key={course.id}>
            <Link to={`/course/${course.id}`}>{course.name}</Link>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default CourseList;



import React, { useEffect, useState } from 'react';
import axios from 'axios';
import { useParams } from 'react-router-dom';

function CourseDetail() {
  const { id } = useParams();
  const [course, setCourse] = useState(null);

  useEffect(() => {
    axios.get(`/api/courses/${id}/`)
      .then(response => {
        setCourse(response.data);
      })
      .catch(error => {
        console.error("There was an error fetching the course details!", error);
      });
  }, [id]);

  if (!course) return <div>Loading...</div>;

  return (
    <div>
      <h1>{course.name}</h1>
      <p

      


