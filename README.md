# mongoengine_serializer
serialize mongoengine documents

### Current Support
serialize ObjectIds and datetime format
> if you want to improve just create a pull 
> request or put a suggestion comment inside 
> issues tab

## Installation
```
pip install mongoengine_serialize
```

# Example:

#### Your mongoengine schema
```
class Users(Document):
    avatar_url = URLField(required=True)
    first_name = StringField(required=True)
    last_name = StringField(required=True)
    email = StringField(required=True)
    password = StringField(required=True)
    created_at = DateTimeField(default=utc_now)
    posts = ReferenceField('Posts')

class Posts(Document):
    content = StringField(required=True)
    comments = ListField(ReferenceField('Comments'))
```

#### Create a Users serializer that extends Serialize class from payload_serializer

```
from payload_serializer import Serialize

user = Users.objects(first_name='Hello').first()
serialized_user = Serialize(user).jsonify()
```

#### Changing data attributes
> Alter data by using alter_after_serialize_attributes function

```
UserSerializer(Serialize):

    def alter_after_serialize_attributes(self, collection):
        if "avatar_url" in collection:
            collection['avatar_url'] = "http://" + collection['avatar_ur']
        elif "posts" in collection:
            collection["posts"]["comments"] = Serialize(collection["posts"]["comments"]).jsonify()


user = Users.objects(first_name='Hello').first()
serialized_user = UserSerializer(user).jsonify()

```
> alter_after_serialize_attributes is called each attribute of the collection.
> the best practice to use it is when you needed a 2nd to 3rd to deapest tier of ObjectId connected to your parent collection.

#### Exclude attributes
> exclude_attribute is called before set_attributes
> it helps on filtering your dictionary before returning
```
Serialize(user).exclude('password', 'created_at').jsonify()
```
