# HashMap in Go


This is a implementation of closed addressing in a typical hashmap we use in go lang.

**some globals & imports**
```go
package main
import "fmt"

const ArraySize = 7
```

### Now lets define our steps

##  Structures

> Hashtable structure
```go
type HashTable struct{ 
// our hashtable will be an array
  array [ArraySize]* bucket
}
```
 > bucket structure (linkedlist)
```go
type bucket struct{
  head * bucketNode
}
```
 > bucketNode structure
```go
type bucketNode struct{
  key string 
  next *bucketNode
}
```

## Hash function

```go
func hash(key string) int{
  sum := 0
  for _,v := range key{
    sum += int(v)
  }
return sum % ArraySize
}
```

## Methods for HashTable

- insert method

```go
//insert will take a key and add it to hash table arr
func (h *HashTable) Insert(key string) {
  index := hash(key)
  h.array[index].insertInBucket(key)
}
```
- search method
```go
//search will take a key and return true if in hash table arr
func (h *HashTable) Search(key string) bool{
   index := hash(key)
  return h.array[index].searchInBucket(key)
}
```
- delete method
```go
//delete will take a key and delete the item from hash table arr
func (h *HashTable) Delete(key string) {
   index := hash(key)
  h.array[index].deleteFromBucket(key)
}
```

## Methods for Bucket(linkedlist)

- insertInBucket function 

```go
func (b * bucket) insertInBucket(k string) {
  if !b.searchInBucket(k){
  newNode := &bucketNode{key : k}
  newNode.next = b.head
  b.head = newNode
    } else {
  fmt.Println(k, "Already exists")
    }
}
```

- searchInBucket function
```go

func (b * bucket) searchInBucket(k string) bool {
   currentNode := b.head
  for currentNode != nil {
    if currentNode.key == k {
      return true
    }
  currentNode = currentNode.next
  }
 return false
}
```
- deleteFromBucket function
```go
func (b*bucket) deleteFromBucket(k string){
   if(b.head.key == k){
    b.head = b.head.next
    return 
   }
  
  previousNode := b.head
  for previousNode.next != nil{
    if previousNode.next.key == k {
      previousNode.next = previousNode.next.next
    }
  previousNode = previousNode.next
  }
}
```

## Init function 
```go
// Init function to initialize hashtable this will create a bucket in
    // each slot of the hashtable
func Init() *HashTable{ 
  result := &HashTable{}

  for i := range result.array {
    result.array[i] = &bucket{}
  }
return result
}
```


## Main function
```go
func main() {
  // myHashTable := &HashTable{} checking step
  myHashTable := Init()
 // fmt.Println(myHashTable)
 //  testBucket := &bucket{}
 //  testBucket.insertInBucket("sangha")

 //  fmt.Println(testBucket.searchInBucket("sangha"))
 //  testBucket.deleteFromBucket("messi")
 //   fmt.Println(testBucket.searchInBucket("sangha"))

 list := []string{
   "ERIC",
   "JASH",
   "JOSEPH",
   "PRAVIN",
   "WASER",
   "MAYUR",
   "QINGSHIN",
 }
 for _,v := range list{
   myHashTable.Insert(v)
 }
fmt.Println(myHashTable.Search("JOSEPH"))//true
fmt.Println(myHashTable.Search("MEGAN"))//false
}
```


### Lastly Full Code
run command: go build main.go 
> //main.go is your filename

```go  
package main
import "fmt"

const ArraySize = 7


// hash function
func hash(key string) int{
  sum := 0
  for _,v := range key{
    sum += int(v)
  }
return sum % ArraySize
}

// Hashtable structure
type HashTable struct{ // our hashtable will be an array
  array [ArraySize]* bucket
}

// bucket structure (linkedlist)
type bucket struct{
  head * bucketNode
}

//bucketNode structure
type bucketNode struct{
  key string 
  next *bucketNode
}

//insert will take a key and add it to hash table arr
func (h *HashTable) Insert(key string) {
  index := hash(key)
  h.array[index].insertInBucket(key)
}

//insertInBucket function 
func (b * bucket) insertInBucket(k string) {
  if !b.searchInBucket(k){
  newNode := &bucketNode{key : k}
  newNode.next = b.head
  b.head = newNode
    } else {
  fmt.Println(k, "Already exists")
    }
}

//search will take a key and return true if in hash table arr
func (h *HashTable) Search(key string) bool{
   index := hash(key)
  return  h.array[index].searchInBucket(key)
}

// searchInBucket function
func (b * bucket) searchInBucket(k string) bool {
   currentNode := b.head
  for currentNode != nil {
    if currentNode.key == k {
      return true
    }
  currentNode = currentNode.next
  }
 return false
}

//delete will take a key and delete the item from hash table arr
func (h *HashTable) Delete(key string) {
   index := hash(key)
   h.array[index].deleteFromBucket(key)
}

// deleteFromBucket function
func (b*bucket) deleteFromBucket(k string){
 if(b.head.key == k){
   b.head = b.head.next
   return 
 }
  
  previousNode := b.head
  for previousNode.next != nil{
    if previousNode.next.key == k {
      previousNode.next = previousNode.next.next
    }
  previousNode =  previousNode.next
  }
}

// Init function to initialize hashtable this will create a bucket in
    // each slot of the hashtable
func Init() *HashTable{ 
  result := &HashTable{}

  for i := range result.array {
    result.array[i] = &bucket{}
  }
return result
}



func main() {
  // myHashTable := &HashTable{} checking step
  myHashTable := Init()
 fmt.Println(myHashTable)
 testBucket := &bucket{}
 testBucket.insertInBucket("sangha")

 fmt.Println(testBucket.searchInBucket("sangha"))
 testBucket.deleteFromBucket("sangha")
 fmt.Println(testBucket.searchInBucket("sangha"))

 list := []string{
  "ERIC",
  "JASH",
  "JOSEPH",
  "PRAVIN",
  "WASER",
  "MAYUR",
  "QINGSHIN",
 }
 for _,v := range list{
   myHashTable.Insert(v)
 }
 fmt.Println(myHashTable.Search("JOSEPH"))//true
 fmt.Println(myHashTable.Search("MEGAN"))//false
}
```