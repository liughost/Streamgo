# Streamgo
* 当过程风格的代码调用关系复杂时，程序员需要谨慎仔细行事，相比较流式风格的代码比较清爽，主干清晰，尤其是应对需求变更的时候优势明显。
* java8里借用lamda表达式实现了一套比较完美的流式编程风格，golang作为一个简洁的语言还没有官方的流式风格的包（可能早就有了，可能是我孤陋寡闻了）有点可惜了。
* 我实现了一套相对比较通用的流式风格的包，实现原理是组成一个任务链表，每一个节点都保存了首节点和下一个节点以及该节点应该执行的回调函数指针，流式任务启动后从第一个节点开始，逐个执行，遇到异常则终止流式任务，直到执行到最后一个，结束任务链。
## 工作原理
* 流式工作原理：
* 各个任务都过指针链表的方式组成一个任务链，这个任务链从第一个开始执行，直到最后一个
* 每一个任务节点执行完毕会将结果带入到下一级任务节点中。
* 每一个任务是一个Stream节点，每个任务节点都包含首节点和下一个任务节点的指针,
* 除了首节点，每个节都会设置一个回调函数的指针，用本节点的任务执行,
* 最后一个节点的nextStream为空,表示任务链结束。
## 导入方式
* go get github.com/liughost/Streamgo
## 使用
* 最后一定要以.Go方法结束,否则不会执行任何方法函数。
* 初始参数是在最后的.Go方法中传入的，这一点和java8的有所不同。
* 所有的函数入参必须是(arg interface{}，出參是 (interface{}, error)
## 例子
``` 
import (
 "github.com/liughost/Streamgo"
 "fmt"
)
//起床
func GetUP(arg interface{}) (interface{}, error) {
    t, _ := arg.(string)
    fmt.Println("铃铃.......", t, "###到时间啦，再不起又要迟到了！")
    return "醒着的状态", nil
}

//蹲坑
func GetPit(arg interface{}) (interface{}, error) {
    s, _ := arg.(string)
    fmt.Println(s, "###每早必做的功课，蹲坑！")
    return "舒服啦", nil
}

//洗脸
func GetFace(arg interface{}) (interface{}, error) {
    s, _ := arg.(string)
    fmt.Println(s, "###洗脸很重要！")
    return "脸已经洗干净了，可以去见人了", nil
}

//刷牙
func GetTooth(arg interface{}) (interface{}, error) {
    s, _ := arg.(string)
    fmt.Println(s, "###刷牙也很重要！")
    return "牙也刷干净了，可以放心的大笑", nil
}

//吃早饭
func GetEat(arg interface{}) (interface{}, error) {
    s, _ := arg.(string)
    fmt.Println(s, "###吃饭是必须的(需求变更了，原来的流程里没有，这次加上)")
    return "吃饱饱了", nil
}

//换衣服
func GetCloth(arg interface{}) (interface{}, error) {
    s, _ := arg.(string)
    fmt.Println(s, "###还要增加一个换衣服的流程！")
    return "找到心仪的衣服了", nil
}

//出门
func GetOut(arg interface{}) (interface{}, error) {
    s, _ := arg.(string)
    fmt.Println(s, "###一切就绪，可以出门啦！")
    return "", nil

}
func main() {
    NewStream().
        Next(GetUP).
        Next(GetPit).
        Next(GetTooth).
        Next(GetFace).
        Next(GetEat).//需求变更了后加上的
        Next(GetCloth).
        Next(GetOut).
        Go("2018年1月28日8点10分")
}
``` 
