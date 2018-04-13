# JSONata Snippets

## Array of objects to single objects

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

The `people` object contains an array of objects with data about users.  The goal is to convert this array to an object with each key being the `name` property and data being the rest of the properties (exluding `name`).  We also notice that the name "John" is there 2 times so we will need to use the `id` property as part of the key to ensure the key is unique.



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
