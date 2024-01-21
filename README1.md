### README.md

#### Project Description
This project is an implementation of a command-line interface for an AirBnB-like application. It includes a command interpreter that allows users to create, show, destroy, update, and list various instances such as BaseModel, User, State, City, Amenity, Place, and Review. The project also includes a file storage system for serializing instances to a JSON file and deserializing instances from the JSON file.

#### Command Interpreter
The command interpreter, implemented in `console.py`, allows users to interact with the application through various commands. It includes functionalities to create, show, destroy, update, and list instances of different classes.

#### How to Start
To start the command interpreter, run the following command in your terminal:
```bash
$ ./console.py
```

#### How to Use
Once the command interpreter is running, you can use the following commands:

- `create`: Creates a new instance of the specified class.
- `show`: Prints the string representation of an instance based on the class name and ID.
- `destroy`: Deletes an instance based on the class name and ID.
- `all`: Prints all string representations of instances based on the class name.
- `update`: Updates an instance based on the class name, ID, attribute name, and attribute value.

#### Examples
```bash
$ create BaseModel
$ show BaseModel 1234-1234-1234
$ destroy BaseModel 1234-1234-1234
$ all BaseModel
$ update BaseModel 1234-1234-1234 email "aibnb@mail.com"
```

#### Unit Tests
To run unit tests, use the following commands:
```bash
$ python3 -m unittest discover tests
$ echo "python3 -m unittest discover tests" | bash
```

### Implementation

#### BaseModel (models/base_model.py)

```python
import uuid
from datetime import datetime

class BaseModel:
    def __init__(self, *args, **kwargs):
        if kwargs:
            for key, value in kwargs.items():
                if key == "__class__":
                    continue
                if key in ["created_at", "updated_at"]:
                    value = datetime.strptime(value, "%Y-%m-%dT%H:%M:%S.%f")
                setattr(self, key, value)
        else:
            self.id = str(uuid.uuid4())
            self.created_at = self.updated_at = datetime.now()

    def save(self):
        self.updated_at = datetime.now()

    def to_dict(self):
        obj_dict = self.__dict__.copy()
        obj_dict["__class__"] = self.__class__.__name__
        obj_dict["created_at"] = self.created_at.isoformat()
        obj_dict["updated_at"] = self.updated_at.isoformat()
        return obj_dict
```

#### FileStorage (models/engine/file_storage.py)

```python
import json

class FileStorage:
    __file_path = "file.json"
    __objects = {}

    def all(self):
        return self.__objects

    def new(self, obj):
        key = "{}.{}".format(obj.__class__.__name__, obj.id)
        self.__objects[key] = obj

    def save(self):
        serialized_objects = {k: v.to_dict() for k, v in self.__objects.items()}
        with open(self.__file_path, "w") as file:
            json.dump(serialized_objects, file)

    def reload(self):
        try:
            with open(self.__file_path, "r") as file:
                data = json.load(file)
                for key, value in data.items():
                    class_name, obj_id = key.split('.')
                    module = __import__("models.{}".format(class_name), fromlist=[class_name])
                    obj = getattr(module, class_name)(**value)
                    self.__objects[key] = obj
        except FileNotFoundError:
            pass
```

#### User (models/user.py)

```python
from models.base_model import BaseModel

class User(BaseModel):
    email = ""
    password = ""
    first_name = ""
    last_name = ""
```

#### Other Classes (models/state.py, models/city.py, models/amenity.py, models/place.py, models/review.py)

Similar to the `User` class, create classes for `State`, `City`, `Amenity`, `Place`, and `Review` following the same structure.

#### console.py

```python
import cmd
from models.base_model import BaseModel
from models.engine.file_storage import FileStorage

class HBNBCommand(cmd.Cmd):
    prompt = "(hbnb) "

    def emptyline(self):
        pass

    def do_quit(self, arg):
        """Quit command to exit the program"""
        return True

    def do_EOF(self, arg):
        """EOF command to exit the program"""
        print("")
        return True

    def help_quit(self):
        print("Quit command to exit the program")

    def help_EOF(self):
        print("EOF command to exit the program")

    def do_create(self, arg):
        """Creates a new instance of BaseModel"""
        if not arg:
            print("** class name missing **")
            return
        try:
            new_instance = eval(arg)()
            new_instance.save()
            print(new_instance.id)
        except NameError:
            print("** class doesn't exist **")

    def do_show(self, arg):
        """Prints the string representation of an instance"""
        # Implement the show command

    def do_destroy(self, arg):
        """Deletes an instance based on the class name and id"""
        # Implement the destroy command

    def do_all(self, arg):
        """Prints all string representation of instances"""
        # Implement the all command

    def do_update(self, arg):
        """Updates an instance based on the class name and id"""
        # Implement the update command

if __name__ == "__main__":
    storage = FileStorage()
    storage.reload()
    HBNBCommand().cmdloop()
```

# Contributors

This project was contributed to by the following individuals:

- **Albertine Ingabire**
  - Email: [a.ingabire1@alustudent.com](mailto:a.ingabire1@alustudent.com)

- **Bavukile Vilane**
  - Email: [b.vilane@alustudent.com](mailto:b.vilane@alustudent.com)

---
