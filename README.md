## 1.介绍

垃圾分类查询管理系统，对不懂的垃圾进行查询进行分类并可以预约上门回收垃圾。\
让用户自己分类垃圾，\
按国家标准自己分类，\
然后在网上提交订单，\
专门有人负责回收，\
统一回收到垃圾处理站，\
然后工人开始再次分类，\
将可再次循环使用的贩卖给工厂（以后有钱自己开）。\
订单处理完（一般7天内），\
将一部分钱返还给用户。\
让垃圾变成钱！

### 1.1 功能点


| 序号 | 功能点   |
| ---- | -------- |
| 1    | 用户管理 |
| 2    | 页面管理 |
| 3    | 角色管理 |
| 4    | 首页     |
| 5    | 贡献管理 |
| 6    | 垃圾管理 |
| 7    | 全国统计 |
| 8    | 搜索记录 |
| 9    | 分类管理 |
| 10   | 分类列表 |
| 11   | 垃圾列表 |
| 12   | 修改奖励 |
| 13   | 我的收益 |
| 14   | 随机数据 |
| 15   | 分类统计 |
| 16   | 投放统计 |
| 17   | 公告管理 |
| 18   | 公告列表 |
| 19   | 发布公告 |
| 21   | 每日垃圾 |
| 22   | 贡献记录 |
| 23   | 预约管理 |


## 2.软件架构

JDK 1.8\
SpringBoot 2.2.6.RELEASE\
freemarker 2.3.28\
mybatis-plus 3.2.0\
shiro 1.3.2

# 运行指导

idea导入源码空间站顶目教程说明(Vindows版)-ssm篇：

http://mtw.so/5MHvZq 

源码地址：[http://codegym.top](http://codegym.top/)。 

## 3.安装启动



![输入图片说明](https://img-blog.csdnimg.cn/img_convert/8c59c998326bf61c2370f2e7f448ccde.png)

启动后访问地址：[http://127.0.0.1:8083/](http://127.0.0.1:8083/)
用户名：**admin**、密码：**123456**
![输入图片说明](https://img-blog.csdnimg.cn/img_convert/ee17126371cb86703e2299fb1fe317f3.png)


## 4.运行截图

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/7ab9bb7724c2965ec0cba646d94dba34.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/b4a60744841b9c6cf47306b6fc00b034.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/47e5ac5d7dce8e4e5ce34c9e95e961da.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/a3334acf661310f46b37a5e80580adf9.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/4d523ea3de28411c3f8098a11f026399.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/398187c993fdaf9f028e08005660b973.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/6cf2bd8adfb64dbff6c403d62d6007f0.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/589ec6f90b20d0ec103d8291158b9294.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/2eaedcf7c5b149baaa42e401492edc57.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/91e217ee13668fb4d80adfa812f38d84.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/f285ce68f44711d9a94881faec23caef.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/c4e04039ba8e803b2e0f57ee6ffbe73e.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/99a4a9f0757d4df27c923f5b53aeb5cb.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/f271cc1bc8307f03185f4bb7088774d3.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/25ccd6f73e35d0b9bbae2ad86a68ccd2.png)

![输入图片说明](https://img-blog.csdnimg.cn/img_convert/b9c9ba198423b79a129c9e8bdca374e8.png)



## 代码
CustomRealm

```java
package com.gcms.shiro;

import com.gcms.mapper.UserMapper;
import com.gcms.mapper.UserRoleMapper;
import com.gcms.pojo.User;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.session.Session;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.subject.Subject;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.util.*;

@Component
public class CustomRealm extends AuthorizingRealm {
	/** 用户信息service */
	private final UserMapper userMapper;
	/** 用户权限service */
	private final UserRoleMapper userRoleMapper;
	/** logback日志记录 */
	private final Logger logger = LoggerFactory.getLogger(CustomRealm.class);

	private static Map<String, Session> sessionMap = new HashMap<>();

	@Autowired
	public CustomRealm(UserMapper userMapper, UserRoleMapper userRoleMapper) {
		this.userMapper = userMapper;
		this.userRoleMapper = userRoleMapper;
	}


	/**
	 * @Override
	 * @see org.apache.shiro.realm.AuthenticatingRealm#doGetAuthenticationInfo(AuthenticationToken)
	 *      <BR>
	 *      Method name: doGetAuthenticationInfo <BR>
	 *      获取身份验证信息 Description: Shiro中，最终是通过 Realm 来获取应用程序中的用户、角色及权限信息的。 <BR>
	 * @param authenticationToken 用户身份信息 token
	 * @return 返回封装了用户信息的 AuthenticationInfo 实例
	 * @throws AuthenticationException <BR>
	 */
	@Override
	protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken)
			throws AuthenticationException {
		// 获取token令牌
		UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;
		// 从数据库获取对应用户名密码的用户
		User user = userMapper.getByName(token.getUsername());
		if (null == user) {
			logger.warn("{}---用户不存在", token.getUsername());
			// 向前台抛出用户不存在json对象
			throw new AccountException("USERNAME_NOT_EXIST");
		}
		String password = user.getPassword();
		if (null == password) {
			logger.warn("{}---用户不存在", token.getUsername());
			// 向前台抛出用户不存在json对象
			throw new AccountException("USERNAME_NOT_EXIST");
		} else if (!password.equals(new String((char[]) token.getCredentials()))) {
			logger.warn("{}---输入密码错误", token.getUsername());
			// 向前台抛出输入密码错误json对象
			throw new AccountException("PASSWORD_ERR");
		}
		logger.info("{}---身份认证成功", user.getName());
		Subject subject = SecurityUtils.getSubject();
		// 设置shiro session过期时间(单位是毫秒!)
		subject.getSession().setTimeout(7_200_000);

		Session s = subject.getSession();
		String uid = user.getId()+"";

		try {
			Session s2 = sessionMap.get(uid);
			if (s2 != null) {
				s2.setTimeout(0);
				sessionMap.remove(s2);
			}
		} catch (Exception e) {
			// 已经退出，但是还是有他。
			sessionMap.remove(s);
		}
		// 把这个人登录的信息给放进全局变量
		sessionMap.put(uid, s);

		return new SimpleAuthenticationInfo(user, password, getName());
	}

	/**
	 * @Override
	 * @see AuthorizingRealm#doGetAuthorizationInfo(PrincipalCollection)
	 *      <BR>
	 *      Method name: doGetAuthorizationInfo <BR>
	 *      Description: 获取授权信息 <BR>
	 * @param principalCollection
	 * @return <BR>
	 */
	@Override
	protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
		// 从shro里面获取用户对象
		User user = (User) SecurityUtils.getSubject().getPrincipal();
		SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
		// 角色列表
		List<String> roles = null;
		// 获得该用户角色
		if (null != user) {
			roles = userRoleMapper.getRoles(user.getId()+"");
		} else {
			logger.warn("用户session失效!");
		}
		Set<String> set = new HashSet<>();
		// 需要将 role 封装到 Set 作为 info.setRoles() 的参数
		for (String role : roles) {
			set.add(role);
		}
		// 设置该用户拥有的角色
		info.setRoles(set);
		return info;
	}
}
```
MyUtils

```java
package com.gcms.utils;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.io.*;
import java.text.Format;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;

/**
 * class name:MyUtils <BR>
 */
public class MyUtils {


	private MyUtils() {
		throw new IllegalStateException("Utility class");
	}

	/** logback日志记录 */
	private static final Logger logger = LoggerFactory.getLogger(MyUtils.class);

	/**
	 * Method name: isEmail <BR>
	 * Description: 判断是不是邮箱,是就返回true <BR>
	 * Remark: <BR>
	 * 
	 * @param email
	 * @return boolean<BR>
	 */
	public static boolean isEmail(String email) {
		String regex = "^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\\.[a-zA-Z0-9_-]+)+$";
		if (email.matches(regex)) {
			return true;
		} else {
			return false;
		}
	}

	/**
	 * Method name: isPhoneNum <BR>
	 * Description: 判断手机号是不是正确,是就返回true <BR>
	 * Remark: <BR>
	 * 
	 * @param phoneNume
	 * @return boolean<BR>
	 */
	public static boolean isPhoneNum(String phoneNume) {
		String pattern = "^((1[3,5,8][0-9])|(14[5,7])|(17[0,6,7,8])|(19[7]))\\d{8}$";
		if (phoneNume.matches(pattern)) {
			return true;
		} else {
			return false;
		}
	}

	/**
	 * Method name: nowDate <BR>
	 * Description: 返回当前日期和时间yyyy-MM-dd HH:mm:ss <BR>
	 * Remark: <BR>
	 * 
	 * @return String<BR>
	 */
	public static String getNowDateTime() {
		String dateTime = "";
		String pattern = "yyyy-MM-dd HH:mm:ss";
		Date date = new Date();
		SimpleDateFormat sdf = new SimpleDateFormat(pattern);
		dateTime = sdf.format(date);
		return dateTime;
	}

	/**
	 * Method name: getNowDateYMD <BR>
	 * Description: 返回当前日期和时间 yyyy-MM-dd <BR>
	 * Remark: <BR>
	 * 
	 * @return String<BR>
	 */
	public static String getNowDateYMD() {
		String dateTime = "";
		String pattern = "yyyy-MM-dd";
		Date date = new Date();
		SimpleDateFormat sdf = new SimpleDateFormat(pattern);
		dateTime = sdf.format(date);
		return dateTime;
	}

	/**
	 * Method name: getNowDateCHYMD <BR>
	 * Description: 返回当前日期和时间 yyyy年MM月dd日<BR>
	 * Remark: <BR>
	 * 
	 * @return String<BR>
	 */
	public static String getNowDateCHYMD() {
		String dateTime = "";
		String pattern = "yyyy年MM月dd日";
		Date date = new Date();
		SimpleDateFormat sdf = new SimpleDateFormat(pattern);
		dateTime = sdf.format(date);
		return dateTime;
	}

	/**
	 * Method name: getAutoNumber <BR>
	 * Description: 根据时间获取编号:年月日+4位数字 <BR>
	 * Remark: 格式:201809200001 <BR>
	 * 
	 * @return String<BR>
	 */
	public static synchronized String getAutoNumber() {
		String autoNumber = "";
		int number = 0;
		String oldDate = "";
		SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMdd");
		String nowDate = sdf.format(new Date());
		File f2 = new File(MyUtils.class.getResource("").getPath());
		String path = f2.getAbsolutePath();

		File f = new File(path + "/date.txt");

		try {
			BufferedReader br = new BufferedReader(new FileReader(f));
			String line = "";
			try {
				line = br.readLine();
				String[] sb = line.split(",");
				oldDate = sb[0];
				if (oldDate.equals(nowDate)) {
					number = Integer.parseInt(sb[1]);
				} else {
					number = 0;
				}
				br.close();
			} catch (IOException e) {
				logger.error("根据时间获取编号出现异常", e);
			}

			autoNumber += nowDate;
			number++;
			int i = 1;
			int n = number;
			while ((n = n / 10) != 0) {
				i++;
			}
			for (int j = 0; j < 4 - i; j++) {
				autoNumber += "0";
			}
			autoNumber += number;

			try {
				BufferedWriter bw = new BufferedWriter(new FileWriter(f));
				bw.write(nowDate + "," + number);
				bw.close();
			} catch (IOException e) {
				logger.error("根据时间获取编号出现异常", e);
			}

		} catch (FileNotFoundException e) {
			logger.error("根据时间获取编号出现异常", e);
		}

		return autoNumber;
	}

	/**
	 * Method name: get2DateDay <BR>
	 * Description: 获取两个日期之间的天数 <BR>
	 * Remark: 如2018-09-01 和 2018-09-017 返回就是17天<BR>
	 * 
	 * @param startDate
	 * @param endDate
	 * @return int<BR>
	 */
	public static int get2DateDay(String startDate, String endDate) {
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
		Date date1 = null;
		Date date2 = null;
		long days = 0;
		try {
			date1 = sdf.parse(startDate);
			date2 = sdf.parse(endDate);
			days = (date2.getTime() - date1.getTime()) / (24 * 3600 * 1000);

		} catch (ParseException e) {
			logger.error("获取两个日期之间的天数出现异常", e);
		}

		return (int) days + 1;
	}

	/**
	 * Method name: toLowCase <BR>
	 * Description: 第一个字母小写 <BR>
	 * Remark: <BR>
	 * 
	 * @param s
	 * @return String<BR>
	 */
	public static String toLowCase(String s) {
		return s.substring(0, 1).toLowerCase() + s.substring(1, s.length());
	}

	/**
	 * Method name: setStartUP <BR>
	 * Description: 第一个字母大写 <BR>
	 * Remark: <BR>
	 * 
	 * @param s
	 * @return String<BR>
	 */
	public static String setStartUP(String s) {
		return s.substring(0, 1).toUpperCase() + s.substring(1, s.length());
	}

	/**
	 * Method name: getUp_ClassName <BR>
	 * Description: 根据表名获取类名不带后缀Bean <BR>
	 * Remark: <BR>
	 * 
	 * @param s
	 * @return String<BR>
	 */
	public static String getUp_ClassName(String s) {
		String cName = "";
		// 首字母大写
		cName = s.substring(1, 2).toUpperCase() + s.substring(2, s.length());

		String[] tem = cName.split("_");
		int len = tem.length;
		cName = tem[0];
		for (int i = 1; i < len; i++) {
			cName += setStartUP(tem[i]);
		}
		// tables.add(cName);//把所有的表添加到这里
		return cName;
	}

	/**
	 * Method name: getFiled2Pro <BR>
	 * Description: 根据字段名获取属性 <BR>
	 * Remark: <BR>
	 * 
	 * @return String<BR>
	 */
	public static String getFiled2Pro(String s) {
		String pName = "";
		String[] tem = s.split("_");

		int len = tem.length;
		pName = tem[0];
		for (int i = 1; i < len; i++) {
			pName += setStartUP(tem[i]);
		}
		return pName;
	}


	/**
	 * Method name: getStringDate <BR>
	 * Description: 根据字符串转成日期类型yyyt-MM-dd <BR>
	 * 
	 * @param time
	 * @return Date<BR>
	 */
	public static Date getStringDate(String time) {
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
		Date date = null;
		try {
			date = sdf.parse(time);
		} catch (ParseException e) {
			logger.error("日期转换出错：", e);
		}
		return date;
	}

	/**
	 * Method name: getStringDate <BR>
	 * Description: 根据字符串转成日期类型yyyt-MM-dd HH:mm:ss<BR>
	 * 
	 * @param time
	 * @return Date<BR>
	 */
	public static Date getStringDateTime(String time) {
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		Date date = null;
		try {
			date = sdf.parse(time);
		} catch (ParseException e) {
			logger.error("日期转换出错：", e);
		}
		return date;
	}

	/**
	 * Method name: getNowDateFirstDay <BR>
	 * Description: 根据系统时间获取当月第一天 <BR>
	 * 
	 * @return String<BR>
	 */
	public static String getNowDateFirstDay() {
		Format format = new SimpleDateFormat("yyyy-MM-dd");
		// 获取当前月第一天：
		Calendar c = Calendar.getInstance();
		c.add(Calendar.MONTH, 0);
		c.set(Calendar.DAY_OF_MONTH, 1);// 设置为1号,当前日期既为本月第一天
		return format.format(c.getTime());
	}

	/**
	 * Method name: getNowDateLastDay <BR>
	 * Description: 根据系统时间获取当月最后一天 <BR>
	 * 
	 * @return String<BR>
	 */
	public static String getNowDateLastDay() {
		Format format = new SimpleDateFormat("yyyy-MM-dd");
		// 获取当前月最后一天
		Calendar ca = Calendar.getInstance();
		ca.set(Calendar.DAY_OF_MONTH, ca.getActualMaximum(Calendar.DAY_OF_MONTH));
		return format.format(ca.getTime());
	}
	
	/**
	 * 根据日期对象获取yyyy年MM月dd字符串
	 * @param date
	 * @return
	 */
	public static String getDate2String(Date date) {
		Format format = new SimpleDateFormat("yyyy年MM月dd日  HH时mm分ss秒");
		return format.format(date);
	}

}

```
