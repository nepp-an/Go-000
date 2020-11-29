学习笔记

题目：我们在数据库操作的时候，比如 dao 层中当遇到一个 sql.ErrNoRows 的时候，
是否应该 Wrap 这个 error，抛给上层。为什么，应该怎么做请写出代码？    
答案：应该抛给上层，使用标准库或者其他基础库的时候基本都同数据库或者外部模块进行交互，这时候出现的错误尽量都要使用
errors.Wrap去包住堆栈信息，中间层透传就好，在最上层打印出来，便于定位和定界问题，或者同其他部门扯皮。

```
package main
import(
    "database/sql"
    "github.com/pkg/errors"

    log "github.com/Sirupsen/logrus"
)

func main() {
    var id = "week_2"
    err := service(id)
    if err != nil {
        log.Errorf("service failed, err is %+v", err)
    }
    return
}

func service(id string) error {
    return biz(id)
}

func dao(id string) error {
    var err = sql.ErrNoRows
    if err == sql.ErrNoRows {
        return errors.Wrap(err, "dao failed, id is "+ id)
    }
    return nil
}

func biz(id string) error {
    return dao(id)
}

```