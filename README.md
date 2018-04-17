# JSONata Snippets

## Array of objects to single object

Consider this data set:

```json
{
  "people":
  [
      {"name": "John", "gender": "male", "id": 1},
      {"name": "Jane", "gender": "female", "id": 2},
      {"name": "Jack", "gender": "unkown", "id": 3},
      {"name": "John", "gender": "male", "id": 4}
  
  ]
}
```

The `people` object contains an array of objects with data about users.  The goal is to convert this array to an object with each key being the `name` property and data being the rest of the properties (excluding `name`).  We also notice that the name "John" is there 2 times so we will need to use the `id` property as part of the key to ensure the key is unique.

We will first want to use the `$map` function that will execute a callback for each element in the array:

```
people ~> $map(function($v) {$v})
```

By returning the value `$v` without modifying it, it returns the people array:

```json
[
  {
    "name": "John",
    "gender": "male",
    "id": 1
  },
  {
    "name": "Jane",
    "gender": "female",
    "id": 2
  },
  ...
]
```

What we want to do now is create a new structure with the name and id as the key:

```
people ~> $map(
   function($v) {
       {
           $v.name & "_" & $v.id: $v
       }

   }
)
```

We combine `$v.name` with an underscore and `$v.id` using the `&` concatenation operator.
The following is returned:

```json
[
  {
    "John_1": {
      "name": "John",
      "gender": "male",
      "id": 1
    }
  },
  {
    "Jane_2": {
      "name": "Jane",
      "gender": "female",
      "id": 2
    }
  }
  ...
]
```

We're almost there, we now want to get rid of the `name` property.  To do so we will use the `$sift` function:

```
people ~> $map(
   function($v) {
       {
           $v.name & "_" & $v.id: $v ~> $sift(
               function($v, $k){$k != "name"}
           )
       }

   }
)
```

The `$sift` function will only return properties that correspond to the expression passed as argument.  Here's the result:

```json
[
  {
    "John_1": {
      "gender": "male",
      "id": 1
    }
  },
  {
    "Jane_2": {
      "gender": "female",
      "id": 2
    }
  }
  ...
]
```

We still have an Array of separate Objects, the goal is to have a single object.  To do this we use the `$merge` funtcion:

```
people ~> $map(
   function($v) {
       {
           $v.name & "_" & $v.id: $v ~> $sift(
               function($v, $k){$k != "name"}
           )
       }

   }
) ~> $merge
```

Our final structure looks like this:

```json
{
  "John_1": {
    "gender": "male",
    "id": 1
  },
  "Jane_2": {
    "gender": "female",
    "id": 2
  },
  "Jack_3": {
    "gender": "unkown",
    "id": 3
  },
  "John_4": {
    "gender": "male",
    "id": 4
  }
}
```
