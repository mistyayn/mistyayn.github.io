# Declare and Initialize

It's been a long time since I've used a strong typed language. After using some code from a tuturial I was encountering a problem and couldn't figure out why.

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
