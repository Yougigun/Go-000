# 学习笔记
## 我们在数据库操作的时候，比如 dao 层中当遇到一个 sql.ErrNoRows 的时候，是否应该 Wrap 这个 error，抛给上层。为什么，应该怎么做请写出代码？

因為dao層只專注在與數據庫中的調用，對於error處理應該wrap起來附帶調用訊息，給上層的 service決定處理方式

'''
dddd
'''

##代碼
```go
// Dao
var (
    NotFoundErr = errors.New("Not Found.")
)

func FindUserByUID(uid string) (user *model.User, err error) {
	err = sqlDB.Table("users").Where("uid = ?", uid).Find(user).Error
	if err != nil {
        // Wrap some concrete info for service
		err = errors.Wrap(err, fmt.Sprintf("find by uid: %v error", uid))
	}
	return
}
```


```go
// Service 层
import "./dbo"
func FindUserByIDService(userID int) (user *model.User, err error) {
	user, err = dao.FindUserByUID(userID)
	if errors.Is(err, dao.NotFoundErr) {
        user = "default-user"
        // decide not to throw an err, instead, give an default user info
		err = nil 
	}
	return
}
```