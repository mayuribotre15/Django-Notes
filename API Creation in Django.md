# API Creation

### Step 1: install django rest  framework application

pip install djangorestframework

### Step 2: Register django rest application in setting.py

INSTALLED_APPS = [‘rest_framework’]

### Step 3: Configure database

DATABASES = {
    
      'default': {
    
      'ENGINE': 'django.db.backends.mysql',
    
      'NAME': 'CURD_API',
    
      'USERNAME': 'root',
    
      'PASSWORD': 'Mayuri@94'
    
      }
}

### Step 4: Create models.py

from django.db import models

#### Create your models here.

class ProductDetails(models.Model):
      no = models.IntegerField(primary_key=True)
      
      name = models.CharField(max_length=35)
      
      quantity = models.IntegerField()
      
      trusted = models.BooleanField()

### Step 5: Create file in application

serializers.py

###Step 6: Convert data from database to JSON string

####serializer.py

from rest_framework import serializers

from .models import ProductDetails

class ProductSerializer(serializers.ModelSerializer):

  class Meta:

        model = ProductDetails
        
        fields = '__all__'

### Step 7: view.py

from django.shortcuts import render

from .models import ProductDetails

from .serializers import ProductDetailsSerializers

from rest_framework.response import Response

from rest_framework import status

from rest_framework.views import APIView

#### Create your views here.

class ProductTable(APIView):

      def get(self, r, *args, **kwargs):
        
          prd_obj = ProductDetails.objects.all()
          
          ser_obj = ProductDetailsSerializers(prd_obj, many=True)
          
          return Response(ser_obj.data)
         
      def post(self, r):
      
          ser_obj = ProductDetailsSerializers(data=r.data)
        
          if ser_obj.is_valid():
              
              ser_obj.save()
            
              return Response(ser_obj.data, status=status.HTTP_201_CREATED)
        
          return Response(ser_obj.errors, status=status.HTTP_400_BAD_REQUEST)

    def put(self, r, pk):
        
          prd_obj = ProductDetails.objects.get(pk=pk)
        
          ser_obj = ProductDetailsSerializers(prd_obj, data=r.data)
          
          if ser_obj.is_valid():
            
              ser_obj.save()
            
              return Response(ser_obj.data, status=status.HTTP_201_CREATED)
          
          return Response(ser_obj.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, r, pk):
        
          prd_obj = ProductDetails.objects.get(pk=pk)
          
          prd_obj.delete()
        
          return Response(status=status.HTTP_204_NO_CONTENT)

### Step 8: urls

from API import views

urlpatterns = [

    path('admin/', admin.site.urls),
    
    path('get/', views.ProductTable.as_view()),
    
    path('post/', views.ProductTable.as_view()),
    
    path('put/<int:pk>/', views.ProductTable.as_view()),
    
    path('delete/<int:pk>/', views.ProductTable.as_view()),
]
