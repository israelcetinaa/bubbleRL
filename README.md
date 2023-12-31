# Bubble request library #

#### This is a library designed to facilitate interaction with the Bubble.io database, allowing you to focus on the logic rather than writing lines and lines of code. It includes all the interaction operations such as GET, POST, DELETE, PUT, PATCH, and each method handles possible errors. I hope it proves to be useful to you. ####

---
### Import the class ###
~~~
from bubbleRL import Bubble
~~~


### Initialize the class ###
You will need to pass the URL and API key of your Bubble application to initialize your object. It's essential to consider which database you want to connect to, as the live data is different from the development (dev) data. Taking this into account, the structure of the URL is as follows:

https://appname.bubbleapps.io/api/1.1/obj
or 
https://appname.bubbleapps.io/versio-test/api/1.1/obj

Do not add a slash at the end.
~~~
from bubbleRL import Bubble
import os

api_key = os.environ["YOUR_API_KEY"]
url_live = "https://appname.bubbleapps.io/api/1.1/obj"
your_object = Bubble(url, api_key)
~~~
Once this is done, you will be able to use the method you need for your code.


---
### Methods ###

* getObject
* getTable
* newObject
* newObjects
* replaceObject
* updateObject
* deleteObject


---

### getObject ###
This method allows you to retrieve a specific object from a specific table, so you will need to have the unique ID of the object to use it. It's important to use the exact table name as it appears in the database, including uppercase, dashes, lowercase, etc.

~~~
from bubbleRL import Bubble
import os

api_key = os.environ["YOUR_API_KEY"]
url_live = "https://appname.bubbleapps.io/api/1.1/obj"
table = "Your_Table_Name"
uid = "Your_Unique_id"
your_object = Bubble(url, api_key)

your_object.getObject(table, uid)
print(your_object.object)

>>>
{
    "value1":"value"
    "value2":{
        "value3":"value"
    }
}
~~~
This method will return a dictionary that you can access any of its properties using brackets.

---

### getTable ###
This method allows you to fetch a complete table from the database or a group of objects that meet certain criteria using a set of constraints. This can be useful when you need a specific group of data to work with or when you need to work with all the data in a table.

~~~
from bubbleRL import Bubble
import os

api_key = os.environ["YOUR_API_KEY"]
url_live = "https://appname.bubbleapps.io/api/1.1/obj"
table = "Your_Table_Name"
constrains = []
your_object = Bubble(url, api_key)

your_object.getTable(table, constrains)
print(your_object.table)

>>>
[
    {"value1":"value"
    "value2":{
        "value3":"value"
    }},
    {"value1":"value"
    "value2":{
        "value3":"value"
    }},...
]
~~~
For the constraints, they are formed as a list of dictionaries with 3 fields:
* Key: the name of the field we want to apply the constraint to (unitname)
* Constraint_type: the type of constraint we want to apply (text contains)
* Value: the value we're looking for (Unit)

Here's an example:
~~~
[{"key": "unitname", "constraint_type": "text contains", "value": "Unit"}, {"key": "unitnumber", "constraint_type": "greater than", "value": "3"}]
~~~
For more details on constraints, you can visit the [Bubble documentation](https://manual.bubble.io/core-resources/api/the-bubble-api/the-data-api/data-api-requests#constraint-types).

---

### newObject ###
This function allows you to create an object in the Bubble database for the desired table. You just need to pass the table name, and the properties should be provided as a dictionary to the function. If the parameter doesn't follow the structure of a dictionary, the function will return an error, indicating that the expected parameter was a dictionary.

~~~
from bubbleRL import Bubble
import os

api_key = os.environ["YOUR_API_KEY"]
url_live = "https://appname.bubbleapps.io/api/1.1/obj"
table = "Your_Table_Name"
properties = {
    "propertie1" : "value",
    "propertie2" : "value"
}
your_object = Bubble(url, api_key)

your_object.newObject(table, properties)
print(your_object.newData)

>>>
"unique_id_of_the_new_element"
~~~
"It's important to mention that if one of the properties of the new object is another object from the database, because it's related, then you should pass the unique ID of the object you want to relate as a string. Similarly, it's possible to pass a file, although it's not highly recommended as Bubble has limited storage. However, it's possible to pass a file by providing the file name and its data in base64 format."


~~~
from bubbleRL import Bubble
import os
from base64 import b64encode

api_key = os.environ["YOUR_API_KEY"]
url_live = "https://appname.bubbleapps.io/api/1.1/obj"
table = "Your_Table_Name"
document = open('my_document.pdf', 'rb')
document_read = document.read()
document_64_encode = base64.b64encode(document_read)
properties = {
    "propertie1" : "value",
    "archive" : {
        "filename" : "my_file.pdf",
        "contents" : document_64_encode
    }
}
your_object = Bubble(url, api_key)

your_object.newObject(table, properties)
print(your_object.newData)

>>>
"unique_id_of_the_new_element"
~~~


---

### newObjects ###
In the same way that the 'newObject' method works, this method can create multiple objects in the database by passing a list of dictionaries with the properties. It will return an array with all the unique IDs.

~~~
from bubbleRL import Bubble
import os

api_key = os.environ["YOUR_API_KEY"]
url_live = "https://appname.bubbleapps.io/api/1.1/obj"
table = "Your_Table_Name"
properties = [
    {
        "propertie1" : "value",
        "propertie2" : "value"
    },
    {
        "propertie1" : "value",
        "propertie2" : "value"
    },...
]
your_object = Bubble(url, api_key)

your_object.newObjects(table, properties)
print(your_object.newObjects)

>>>
["id1", "id2", ...]
~~~


---


### replaceObject ###
This method corresponds to a PUT request, which means that special care must be taken when using it, as this method replaces all the fields of an existing object. If some properties are not included in the dictionary, it will result in leaving them empty in the database, the method will return the status code of the request, 204 in case of success, otherwise, it will return the error message returned by the API.

~~~
from bubbleRL import Bubble
import os

api_key = os.environ["YOUR_API_KEY"]
url_live = "https://appname.bubbleapps.io/api/1.1/obj"
table = "Your_Table_Name"
unique_id = "unique_id"
properties = {
    "propertie1" : "value",
    "propertie2" : "value"
}
your_object = Bubble(url, api_key)

your_object.replaceObject(table, unique_id, properties)
print(your_object.replacedObject)

>>>
204
~~~

---

### updateObject ###
This method corresponds to the PATCH request, so only the properties passed to it will be updated. If any property is not included in the dictionary, it will not be changed. This method is particularly useful when you need to update only certain fields. The method will respond with a 204 in case of a successful update and, in case of any issues, it will return the error message.

~~~
from bubbleRL import Bubble
import os

api_key = os.environ["YOUR_API_KEY"]
url_live = "https://appname.bubbleapps.io/api/1.1/obj"
table = "Your_Table_Name"
unique_id = "unique_id"
properties = {
    "propertie1" : "value",
    "propertie2" : "value"
}
your_object = Bubble(url, api_key)

your_object.updateObject(table, unique_id, properties)
print(your_object.updatedObject)

>>>
204
~~~

---

### deleteObject ###
This method allows you to delete an object from the database, and once executed, it cannot be reversed. Please use it with special caution, especially in a production environment.
This method may return 204 if the deletion is successful, but it will return a 404 if the object is not found.

~~~
from bubbleRL import Bubble
import os

api_key = os.environ["YOUR_API_KEY"]
url_live = "https://appname.bubbleapps.io/api/1.1/obj"
table = "Your_Table_Name"
unique_id = "unique_id"
your_object = Bubble(url, api_key)

your_object.deleteObject(table, unique_id)
print(your_object.deletedObject)

>>>
204
~~~