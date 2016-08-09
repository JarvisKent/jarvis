title: Android开发MVP模式入门
date: 2015-12-29 17:36:59
categories: [Android]
tags: [android,MVP]
---
MVP是从MVC演变过来的。M即Model层负责提供数据，V即View层负责显示，P指Presenter层负责逻辑处理。在MVP中View不直接使用Model，它们通过Presenter来通信，它们之间的交互都在Presenter内部进行。实现了View和Model的耦合。<!--more-->

在MVP中，目前体会到的有点有:

- 因为Presenter把Model和View进行了完全的分离。逻辑都放在Presenter里实现。而Presenter是通过定义好的接口与View进行交互的，从而使得View出现改变的时候，Presenter也可以保持不变！

- 在测试方面，因为逻辑都是写在Presenter当中的，我们完全可以在Model和View的接口实现还没有具体内容的时候，就对presenter中的逻辑进行测试。

- 甚至，我们也可以把Presenter可以用于多个视图，而不需要改变Presenter的逻辑。

- 不过，若是Presenter与某个View的联系过于紧密，往往View有大的变更的话，Presenter也是需要变更的。

接下来，我们通过一个简单的一睹MVP的真容吧。首先，有个需求,需要一个登陆界面，用户需要输入用户名和密码后点击登录按钮进行登录。

简单的做一个界面：

![界面](http://7xpi7i.com1.z0.glb.clouddn.com/mvp_%E7%95%8C%E9%9D%A2%E6%95%88%E6%9E%9C%E5%9B%BE.jpg)

创建项目后，为了便于理解，我们把包结构分成了三层，如下:

![包结构图](http://7xpi7i.com1.z0.glb.clouddn.com/mvp_%E5%8C%85%E7%BB%93%E6%9E%84.jpg)

- 定义一个view接口:
```
/**
 * 这边定义页面需要的接口
 * 比如登录页面，用户名和密码的输入框的获取和输入的方法，按钮的点击方法及登录失败提示方法
 * Created by Administrator on 2015/12/30.
 */
public interface ILoginView {

    void setUserName(String userName);
    void setPassword(String password);
    String getUserName();
    String getPassword();
    void loginClick(onclickListen listener);
}
```

- 在Activity对接口进行实现，代码如下：

```
public class LoginActivity extends Activity implements ILoginView {
    EditText userName,password;
    Button login;
    private onclickListen listener;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.layout);

        initView();

    }

    private void initView() {
        userName = (EditText) findViewById(R.id.user);
        password = (EditText) findViewById(R.id.pwd);
        login = (Button) findViewById(R.id.btn_login);
        login.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                listener.click();//自定义的按钮监听接口
            }
        });
    }


    @Override
    public void setUserName(String userNames) {
        userName.setText(String.valueOf(userNames));
    }

    @Override
    public void setPassword(String passwords) {
        password.setText(String.valueOf(passwords));
    }

    @Override
    public String getUserName() {
        if (TextUtils.isEmpty(userName.getText().toString().trim()))
            return null;
        return userName.getText().toString().trim();
    }

    @Override
    public String getPassword() {
        if (TextUtils.isEmpty(password.getText().toString().trim()))
            return null;
        return password.getText().toString().trim();
    }

    @Override
    public void loginClick(onclickListen listener) {
        this.listener = listener;
    }

    @Override
    public void showError() {
        Toast.makeText(this,"账号或密码输入错误",Toast.LENGTH_SHORT).show();
    }
```

这边的按钮监听事件也是自己定义的一个接口，代码如下

```
public interface onclickListen {
    void click();
}
```


- view成的实现到这边就完成，接着是model的实现，我们要创建一个login的实现接口，

```
/**
 * model中的接口，登录功能主要是实现根据账号密码验证是否正确。
 */
public interface ILoginOnServer {

    boolean login(String userName,String userPassword);
}

```

- 实现接口方法

```
public class LoginOnServer implements ILoginOnServer{

    @Override
    public boolean login(String userName, String userPassword) {
        //在这边实现验证的功能
        return true;
    }
}
```

- 最后，在presenter中实现业务逻辑

```
**
 * 逻辑业务都在这边实现
 */
public class LoginPresenter {
    private ILoginView loginView;
    private ILoginOnServer loginOnServer;
    public LoginPresenter(ILoginView view){
        loginView = view;
        userLogin();
    }
    
    private void userLogin() {
        loginOnServer = new LoginOnServer();
        boolean isTrue = loginOnServer.login(loginView.getUserName(),loginView.getPassword());
        if(isTrue){//通过验证进入首页

        }else{//验证失败提示
            loginView.showError();
        }
    }

}
```

从这个简单的案例可以看出，MVP模式是把逻辑业务提取到Presenter中实现，避免view层中的代码过于杂乱。

这篇文章之所以是入门篇，是因为这只是大致介绍下MVP的概念，真正实战开发的时候还需要考虑到其他问题，比如Activity的生命周期以及异常重启的情况等情况。