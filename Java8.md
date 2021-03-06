# Java 8

## Lambdas

#### Inline one line methods 

`(Resource resource) -> resource.getPath().contains("/content/dam");`

#### With Brackets (sometimes requires return statement)
```java
(Page page) -> {
    if (page.isHideInNav()) {
        return null;
    } else {
        return page.getContentResource().adaptTo(BlogPost.class);
    }
};
```

#### Method Reference (For pre-defined methods)
```java
StringUtils::isEmpty;
StringUtils::capitalize;
```
> Variables used in Lambdas have to be effectively `final` 

### Basic Type: Function
- Typed Variable with 2 Types needed
  - T : Type to pass in as a parameter
  - R : Return type
- BiFunction Exists with 2 parameter Types needed.
```java
Function<Page, BlogPost> pageObjectFunction =
(Page page) -> {
    if (page.isHideInNav()) {
        return null;
    } else {
        return page.getContentResource().adaptTo(BlogPost.class);
    }
};
BlogPost post = pageObjectFunction.apply(getCurrentPage());
```

### Consumer
- Used for doing tasks on an object without needing to return anything
- Typed Variable with 1 Type
  - T : Type to pass in as a parameter
```java
Consumer<Page> pageConsumer = (Page page) -> path = page.getPath();
pageConsumer.accept(getCurrentPage());
```


### Supplier
- Used for returning a specific Type
- Typed Variable with 1 Type
  - T : Type to return
```java
Supplier<Date> getPublishDate = bookModel::getPublishDate;
Date date = getPublishDate.get();
```

### Predicate
- Used for filtering based on a single parameter, must return a Boolean
- Typed Variable with 1 Type
  - T : Type to filter on
```java
Predicate<Resource> predicate =
    (Resource resource) -> resource.getPath().startsWith("/content/dam");
boolean test = predicate.test(getResource());
```
  
### Single Method Interfaces
- Can turn Lambdas into Interfaces with one method.
```java
public interface Comparator<String> {
  int compare(String o1, String o2);
}
Comparator<String> stringComparator = (stringMain, stringOther) -> 
Integer.compare(stringMain.length(), stringOther.length());
stringComparator.compare("hello", "world");
```

## Streams
#### Create a Stream
```java
// Lists
ArrayList arrayList = new ArrayList();
arrayList.stream();
// Maps
Map map = new HashMap();
map.entrySet().stream();
// Arrays
String[] array = new String[10];
Arrays.stream(array);
// Iterables
Iterable<Resource> children = resource.getChildren();
StreamSupport.stream(children.spliterator(), false); //parallel or not
```

### Filtering
- Filters objects in a stream based on attributes of that object.
- Takes a Predicate with type of current object in stream
```java
.filter(Objects::nonNull); // Filters out null items
.filter((item) -> item.getAuthor());//Filters out null author books
```

    
### Mapping
- Maps the current stream object type to a different type based on a function
- Takes a `Function<T,R>` where `T`  is the current Type and `R` is the new Type 
```java
.map(BlogPost::getAuthor); //Takes Blog Post, returns String
.map(StringUtils::capitalize);// Takes String, returns String
```

### Sorting
- Sorts the objects in the Stream based on a Comparator
- The `Comparator` class has many static functions to sort based on properties
- Can take normal Comparators as well as Lambda Interfaces
```java
.sorted(Comparator.comparingInt(String::length)); //Sorts by length
.sorted((a, b) -> a.getAuthor().compare(b.getAuthor())); //Sorts by Author
```

### Collecting
- Collects the results of the stream into a collection variable
- Generally collects them to Lists, but also can join as a single String, or get a Map.
```java
//Collects as a Map
.collect(Collectors.toMap(Resource::getPath, Resource::getName));
//Collects as a List
.collect(Collectors.toList());
```

### Reducing
- Reduces the results into a single Variable
- Uses an accumulator method that takes in 2 parameters and returns a variable all of the same type
```java
//Reduce a Stream of integers into a sum.
.reduce(0, Integer::sum);
//Reduce a Stream of integers into the max.
.reduce(0, Integer::max);
```

### Extra Methods
```java
distinct() //filters out duplicate items
limit(5)  //limit results to a certain count.
max()  //and min() //reduces numbers to the max or min
boxed() //converts primitives to boxed classes
```

## Optionals

### Basic Methods

_Optional\<T\>_
- This is the Typed Wrapper for Optional. You supply a Type that the optional can be.

_Optional.empty()_
- The Java 8 version of null. 
- Return this when you would normally return null.

_Optional.ofNullable(object)_ 
- This is the method that wraps an object as a Optional.
> Object can be null.
 
_Optional.of(object);_
- This is the method that wraps an object as a Optional.
> Object cannot be null.
 
 
_Optional.get()_
- Returns the value inside of the Optional.
> This will throw an error if Optional is empty
 
 
_Optional.isPresent()_
- Returns a Boolean whether or not Optional is empty or not.
> Must be called before  `Optional.get()`
 

### Advanced Methods

_ifPresent()_
- This method takes a `Consumer<Object>` and calls it if the object is non null
- Useful for setting fields from Optionals.

`Optional.empty().ifPresent(object -> field = object.property);`

_orElse()_
- This method returns another object if the Optional is empty

`Optional.empty().orElse(otherObject)`

_orElseThrow()_
- This method throws an exception if the Optional is empty

`Optional.empty().orElseThrow(NullPointerException::new);`

_map()_
- This method will call map and return an Optional of the new type.

`Optional.of(object).map(object::getPage).map(page::getName)`

_flatMap()_
- This method is similar to Map, but will unwrap a double Optional.

`Optional.of(object).map(object::getOptionalPage).map(page::getName)`
