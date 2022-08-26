---
layout: default
---

# Declare and Initialize

It's been a long time since I've used a strong typed language. After using some code from a <a href="https://www.youtube.com/watch?v=jFfo23yIWac&list=RDCMUC8butISFwT-Wl7EV0hUK0BQ&start_radio=1&rv=jFfo23yIWac&t=56">tutorial</a> (<a href="https://github.com/AkhilSharma90/Golang-MySQL-CRUD-Bookstore-Management-API">github repo</a>) I was encountering a problem and couldn't figure out why.

Here is the bit of code I started from
```
func CreateBook(w http.ResponseWriter, r *http.Request){
	CreateBook := &models.Book{}
	utils.ParseBody(r, CreateBook)
	b:= CreateBook.CreateBook()
	res, _ := json.Marshal(b)
	w.WriteHeader(http.StatusOK)
	w.Write(res)
}
```

Then I modified it:

```
func CreateUser(w http.ResponseWriter, r *http.Request){
    CreateUser := &models.User{}
    utils.ParseBody(r, CreateUser)
    returnUser, _:= models.GetUserByUniqueId(CreateUser.UniqueID)
    if returnUser.UniqueID == ""{
        returnUser := CreateUser.CreateUser()
    } 
    res, _ := json.Marshal(returnUser)
    w.WriteHeader(http.StatusOK)
    w.Write(res)
}
```

However, I kept getting a returnUser not initialized error but I couldn't figure out why. 

I reached out to a senior developer and he pointed out that the := is declare and initialized. So when inside the if statement I returnUser := CreateUser.CreateUser() I am actually re-initializing the object. As soon as I removed the : it worked!

Here is the final bit of code:

```
func CreateUser(w http.ResponseWriter, r *http.Request){
    CreateUser := &models.User{}
    utils.ParseBody(r, CreateUser)
    returnUser, _:= models.GetUserByUniqueId(CreateUser.UniqueID)
    if returnUser.UniqueID == ""{
        returnUser = CreateUser.CreateUser()
    } 
    res, _ := json.Marshal(returnUser)
    w.WriteHeader(http.StatusOK)
    w.Write(res)
}
```

# Returning gorm.DB Object

After going through a really good <a href="https://www.youtube.com/watch?v=jFfo23yIWac&list=RDCMUC8butISFwT-Wl7EV0hUK0BQ&start_radio=1&rv=jFfo23yIWac&t=56">tutorial</a> (<a href="https://github.com/AkhilSharma90/Golang-MySQL-CRUD-Bookstore-Management-API">github repo</a>) I realized I didn't understand parts of the code that used the gorm.DB package.

This is using <a href="http://github.com/jinzhu/gorm">gorm</a>

The fir of code in questions was:

```
func GetUserByUniqueId(UniqueID string) (*User, *gorm.DB){
	var getUser User
	db:=db.Where("Unique_ID=?", UniqueID).Find(&getUser)
	return &getUser, db
}
```

I didn't understand the purpose of returning the gorm.DB object. What I figured out was that in the tutorial the GetUserByID func which I had copied and modified in order to create this bit of code was also being used in an update function. returning the gorm.DB object faciliated being able to update the object in a different part of the code. Removing the return of this object didn't break my code because I was planning on updating the record in a different way. 

# gorm.db Object Create

I learned an interesting bit of information from this little chunk of code:

This is using <a href="http://github.com/jinzhu/gorm">gorm</a>

```
func (b *User) CreateUser() *User{
	db.NewRecord(b)
	db.Create(&b)
	return b
}
```

After creating a unique constraint for one of the values in the user table I discovered that if I try and insert a duplicate record the user object will still get populated.

With one exception: I have a another primary key ID in my table (no judging this was for learning purposes). What I learned is that the record that gets returned has all the columns populated except for the primary key. Not the end of the world but insteresting information.


