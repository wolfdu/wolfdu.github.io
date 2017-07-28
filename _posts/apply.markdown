apply:

    function Person(name, age) {
        //定义一个类，人类
        this.name = name;
        //名字
        this.age = age;
        //年龄
        this.sayhello = function() {
            console.log("hello")
        }
        ;
    }
    function Print() {
        //显示类的属性
        this.funcName = "Print";
        this.show = function() {
            var msg = [];
            for (var key in this) {
                if (typeof (this[key]) != "function") {
                    msg.push([key, ":", this[key]].join(""));
                }
            }
            console.log(msg.join(" "));
        }
        ;
    }
    function Student(name, age, grade, school) {
        //学生类
        Person.apply(this, arguments);
        Print.apply(this, arguments);
        this.grade = grade;
        //年级
        this.school = school;
        //学校
    }
    var p1 = new Person("jake",10);
    p1.sayhello();
    var s1 = new Student("tom",13,6,"清华小学");
    s1.show();
    s1.sayhello();
    console.log(s1.funcName);
