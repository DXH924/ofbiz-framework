# OFBIZ minilang

来源：

https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=50233697

https://cwiki.apache.org/confluence/display/OFBIZ/Mini+Language+-+minilang+-+simple-method+-+Reference

## Introduction

* In business software there are things that are done hundreds or  thousands of times in a single application that a Mini-Language can  simplify to the point of cutting implementation and maintenance times  not by just 50%, but often as much as 70-90%. Yes, certain tasks will  take only 10% of the effort and knowledge to perform. This makes it  easier people not familiar with the software to manipulate existing or  build new functionality. 
* Mini-Languages tend to have instructions that are more like method calls in a general purpose language. They are meant to solve a specific  problem in a specific context, and are generally worthless or need to be modified for other contexts or problems. 

## Simple Method Overview

* Simple Method Mini-Language 是一种实现由 Control Servlet 调用的事件或由服务引擎调用的服务的简单方法。 可以通过 SimpleMethod 类上的静态方法调用简单方法，也可以通过控制器配置 XML 文件中的条目作为事件调用，如下所示： 

  ```xml
  <event type="simple" path="org/ofbiz/commonapp/workeffort/workeffort/WorkEffortSimpleEvents.xml" invoke="update"/>
  ```

  或者作为服务在sevices.xml中像下面这样配置：

  ```xml
   <service name="createPartyRole" engine="simple"
   location="org/ofbiz/commonapp/party/party/PartyRoleServices.xml" invoke="createPartyRole" auth="true">
   <description>Create a Party Role (add a Role to a Party)</description>
   <attribute name="partyId" type="String" mode="IN" optional="true"/>
   <attribute name="roleTypeId" type="String" mode="IN" optional="false"/>
   </service>
  ```

  



## Special Context Access Syntax

* In strings an and field names a special syntax is supported to flexibly access Map member, List elements and to insert enviroment values into string constants.
* You can use the "."(dot) syntax to access Map members. For example, if you specify the attribute field-name="product.productName" it will reference the productName member of the product Map.
* This would be the same as specifying map-name="product" field-name="productName".
* For example if you have use the attribute field-name="products.widget.productName" it will reference the productName in the widget Map which is in the products Map.



## The simple-method element

*  A simple method can be called in either an event context from the Control
   Servlet (or another event) or in a service context through the Service
   Engine, or any other component that has access to a service dispatcher. 