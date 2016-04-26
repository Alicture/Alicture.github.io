---
author: Alicture
comments: true
layout: post
category: other
publised: true
title: 浅谈BaseServlet
category: javaweb
tag: javaweb
description: 浅谈BaseServlet并且使用代码实例来做示范，分析BaseServlet的优点。

---
BaseServlet是一个很实用的工具。

使用方法：创建Servlet时继承BaseServlet这个好爸爸。

原理：使用了java的反射机制，使其可以调用一个servlet中包含的不同方法。

下面给出一个很棒的BaseServlet。


``` java 

package cn.tslanpu.test.utils;
import java.io.IOException;
import java.lang.reflect.Method;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import cn.itcast.filter.GetRequest;
public class BaseServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
	/**
	 * BaseServlet用来作为其他Servlet的父类
	 * @author Alicture
	 *  继承BaseServlet的Servlet里可有多个方法。
	 *  原行=返回值类型 + 方法名称 + 参数列表
	 */
	@Override
	public void service(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		if(request.getMethod().equalsIgnoreCase("get")){
			request = new GetRequest(request, "utf-8");	
		}else{
			request.setCharacterEncoding("utf-8");//处理post请求编码
		}
		response.setContentType("text/html;charset=UTF-8");//处理响应编码
		
		/**
		 * 1. 获取method方法，他是用户想调用的方法
		 * 2. 把方法名称变为Method类的实例
		 * 3. 通过invoke来调用
		 */
		String methodName = request.getParameter("method");
		Method method = null;
		/**
		 * 2.通过方法名称获取method对象
		 */
		try {
			method = this.getClass().getMethod(methodName,
					HttpServletRequest.class, HttpServletResponse.class);
		} catch (Exception e) {
			throw new RuntimeException("您调用的方法名" + methodName + "他不存在！", e);
		}
		
		/**
		 * 3. 通过method方法调用
		 */
		try {
			String result = (String)method.invoke(this, request, response);
			if(result != null && !result.trim().isEmpty()) {
				int index = result.indexOf(":");
				if(index == -1) {
					request.getRequestDispatcher(result).forward(request, response);
				} else {
					String start = result.substring(0, index);
					String path = result.substring(index + 1);
					//d:开头则转发，r：开头则跳转。
					if(start.equals("d")) {
						request.getRequestDispatcher(path).forward(request, response);
					} else if(start.equals("r")) {
						response.sendRedirect(request.getContextPath() + path);
					}
				}
			}
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}
}

```
>BaseServlet的优点

* 可以减少servlet的数量。传统的一个servlet只能做一件事。继承BaseServlet之后就很强大了。
* 使得业务逻辑更明了。相互之间有联系的方法放在一个servlet中，也方便了维护。
* 是一种优秀的编程思想。