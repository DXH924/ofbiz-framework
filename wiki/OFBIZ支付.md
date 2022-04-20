# OBFIZ支付

## 涉及的数据库表

* ORDER_PAYMENT_PREFERENCE： 存储了一个订单的支付信息 
* PAYMENT_METHOD_TYPE： 支付类型，比如信用卡支付、现金支付、支票支付 
* PAYMENT_METHOD： 支付方法，这个对象很多开发人员将它与PAYMENT_METHOD_TYPE搞混 。 PAYMENT_METHOD是PAYMENT_METHOD_TYPE的具体形式。看字段，其中PARTY_ID代表支付的人（或公司），GL_ACCOUNT_ID代表该支付方法对应COMPANY（在ofbiz中COMPANY指的就是拥有这个ofbiz的企业）的财务哪个科目，FIN_ACCOUNT_ID代表具体的财务账户（如某个银行的账户） 
* PAYMENT：

## build.xml说明

目前的官方文档都是写的在accounting/build.xml中配置payment服务，但是新版的OBFIZ使用gradle管理项目，所以在build.gradle里面配置payment服务



## 第三方支付和运输配置

来源：https://cwiki.apache.org/confluence/display/OFBIZ/Third+Party+Payment+and+Shipment+Configuration

* 第三方支付可以通过实体（ PaymentGatewayConfigType, PaymentGatewayConfig, PaymentGatewaySagePay,  PaymentGatewayAuthorizeNet, PaymentGatewayCyberSource,  PaymentGatewayPayflowPro, PaymentGatewayPayPal,  PaymentGatewayClearCommerce, PaymentGatewayWorldPay,  PaymentGatewayOrbital ）进行配置。此前是通过payment.properties文件进行设置，现在已被弃用

* 这里以PayFlow为例，进行第三方支付的配置说明

  * 添加payflow的第三方依赖

  * 在accounting的build.xml文件中配置：

    ```xml
    <fileset dir="../../hot-deploy/customcomponent/lib" includes="*.jar"/>
    ```

  * 在build.xml中添加注释：

    ```xml
    <!--exclude name="org/ofbiz/accounting/thirdparty/verisign/**"/-->
    ```

  * Prepare configuration data as following with valid details for vendor,  userId, pwd and partner. Other settings can also be changed as per  requirement:

    ```xml
    <PaymentGatewayPayflowPro paymentGatewayConfigId="PAYFLOWPRO_CONFIG" certsPath=" " hostAddress="pilot-payflowpro.paypal.com" hostPort="443" timeout="80" proxyAddress="" proxyPort="80" proxyLogon="" proxyPassword=""
            vendor="" userId="" pwd="" partner="" checkAvs="N" checkCvv2="N" preAuth="Y" enableTransmit="true" logFileName="${sys:getProperty('ofbiz.home')}/runtime/logs/payflow_java.log" loggingLevel="1" maxLogFileSize="100000000" stackTraceOn="Y"/>
    ```

  * hostAddress for staging and production are different so to handle this  case better two separate data file should be created and two entries  should be made in ofbiz-component.xml file with different custom readers like "ext-stag" and "ext-prod" to avoid any confusion while taking  system from staging(testing) to production as shown bellow: 

    ```xml
    <entity-resource type="data" reader-name="ext-stag" loader="main" location="data/CustomComponentConfigurationStagingData.xml"/>
    <entity-resource type="data" reader-name="ext-prod" loader="main" location="data/CustomComponentConfigurationProductionData.xml"/>
    ```
  
  *  Prepare product store payment configuration data with valid  productStoreId and paymentGatewayConfigId created in last step as  follows: 
  
    ```xml
    <ProductStorePaymentSetting productStoreId="9000" paymentMethodTypeId="CREDIT_CARD" paymentServiceTypeEnumId="PRDS_PAY_AUTH" paymentService="" paymentCustomMethodId="CC_AUTH_PAYFLOW" paymentGatewayConfigId="PAYFLOWPRO_CONFIG"/>
    <ProductStorePaymentSetting productStoreId="9000" paymentMethodTypeId="CREDIT_CARD" paymentServiceTypeEnumId="PRDS_PAY_RELEASE" paymentService="" paymentCustomMethodId="CC_RELEASE_PAYFLOW" paymentGatewayConfigId="PAYFLOWPRO_CONFIG"/>
    <ProductStorePaymentSetting productStoreId="9000" paymentMethodTypeId="CREDIT_CARD" paymentServiceTypeEnumId="PRDS_PAY_CAPTURE" paymentService="" paymentCustomMethodId="CC_CAPTURE_PAYFLOW" paymentGatewayConfigId="PAYFLOWPRO_CONFIG"/>
    <ProductStorePaymentSetting productStoreId="9000" paymentMethodTypeId="CREDIT_CARD" paymentServiceTypeEnumId="PRDS_PAY_REAUTH" paymentService="" paymentCustomMethodId="CC_AUTH_PAYFLOW" paymentGatewayConfigId="PAYFLOWPRO_CONFIG"/>
    <ProductStorePaymentSetting productStoreId="9000" paymentMethodTypeId="CREDIT_CARD" paymentServiceTypeEnumId="PRDS_PAY_REFUND" paymentService="" paymentCustomMethodId="CC_REFUND_PAYFLOW" paymentGatewayConfigId="PAYFLOWPRO_CONFIG"/>
    ```
  
  *  Now load data command with following command if you are loading data first time: 
  
    ```
    ant run-install-readers -Ddata-readers=seed,seed-initial,ext,ext-stag or ant run-install-readers -Ddata-readers=seed,seed-initial,ext,ext-prod depending on the data need based on system.
    ```
  
    Now do class path entry for lib as follows in ofbiz-compoent.xml file of your custom component
  
  *  A clean build should be done before starting testing. 

## Payment Services

来源：https://cwiki.apache.org/confluence/display/OFBENDUSER/Payment+Services

* payment service有两种形式：

  *  一种是直接处理gateway，并在内部对 ofbiz 进行 CC 授权，如 payflow 
  * 另一种是支付过程（如获取CC信息和验证支付）在OFBIZ外部完成，如paypal

* service_paymentmethod.xml

  * 对应org.ofbiz.accounting.payment.PaymentGatewayServices

  * 这里对应productstores付款界面中的服务，以下服务用于演示数据， 以允许在不实际通过支付处理器（如 payflow）的情况下处理销售 

    ```
    Credit Card Payment  Authorization Service alwaysApproveCCProcessor 
    Credit Card Payment Capture Service testCCCapture  (declines all orders < 100.00; approves all orders >= 100.00) 
  Credit Card Payment Re-Authorization Service alwaysApproveCCProcessor 
    ```
  
  *  上述的每一个都需要更改为您正在使用的支付处理器的服务，除非它被标记为像贝宝这样的外部支付系统 ，例如在IcsPaymentServices.java中：
  
    ```
    Credit Card Payment  Authorization Service ccAuth 
    Credit Card Payment Capture Service ccCapture 
    Credit Card Payment Re-Authorization Service ccReAuth 
    ```
  
  * 以下services被用于测试，它们可以用在AuthorizationService和Re-Authorization中：
  
    ```
    testRandomAuthorize
    alwaysApproveCCProcessor
    alwaysApproveWithCaptureCCProcessor
    alwaysDeclineCCProcessor
    alwaysNsfCCProcessor
    testCCProcessor
    testCCProcessorWithCapture
    ```
  
  * 以下services可以被用于Capture Service中：
  
    ```
    testCCCaptureWithReAuth - this one actually calls PaymentGatewayServices.getAuthTransaction
    testCCProcessorCaptureAlwaysDecline
    testCCRelease
    testCCRefund
    testCCRefundFailure
    alwaysBadExpireCCProcessor
    badExpireEvenCCProcessor
    alwaysBadCardNumberCCProcessor
    alwaysFailCCProcessor
    ```
  
    
  
* PaymentMethodServices

  ```
  deletePaymentMethod(DispatchContext, Map)
  makeExpireDate(DispatchContext, Map)
  createCreditCard(DispatchContext, Map)
  updateCreditCard(DispatchContext, Map)
  clearCreditCardData(DispatchContext, Map)
  createGiftCard(DispatchContext, Map)
  updateGiftCard(DispatchContext, Map)
  createEftAccount(DispatchContext, Map)
  updateEftAccount(DispatchContext, Map)
  ```

* PaymentGatewayServices

  ```
  authOrderPaymentPreference(DispatchContext, Map)
  authOrderPayments(DispatchContext, Map)
  authPayment(LocalDispatcher, GenericValue, OrderReadHelper, GenericValue, BigDecimal, boolean, BigDecimal)
  getPaymentSettings(GenericValue, GenericValue, String, boolean)
  getPayToPartyId(GenericValue)
  getBillingInformation(OrderReadHelper, GenericValue, Map)
  releaseOrderPayments(DispatchContext, Map)
  releaseOrderPaymentPreference(DispatchContext, Map)
  processReleaseResult(DispatchContext, Map)
  capturePaymentsByInvoice(DispatchContext, Map)
  captureOrderPayments(DispatchContext, Map)
  processCaptureSplitPayment(DispatchContext, Map)
  captureBillingAccountPayment(DispatchContext, Map)
  captureBillingAccountPayments(DispatchContext, Map)
  capturePayment(DispatchContext, GenericValue, OrderReadHelper, GenericValue, BigDecimal)
  capturePayment(DispatchContext, GenericValue, OrderReadHelper, GenericValue, BigDecimal, GenericValue)
  saveError(LocalDispatcher, GenericValue, GenericValue, Map, String, String)
  storePaymentErrorMessage(DispatchContext, Map)
  processResult(DispatchContext, Map, GenericValue, GenericValue)
  processAuthResult(DispatchContext, Map, GenericValue, GenericValue)
  processAuthResult(DispatchContext, Map)
  needsNsfRetry(GenericValue, Map, GenericDelegator)
  processAuthRetryResult(DispatchContext, Map, GenericValue, GenericValue)
  processCaptureResult(DispatchContext, Map, GenericValue, GenericValue)
  processCaptureResult(DispatchContext, Map, GenericValue, GenericValue, String)
  processReAuthFromCaptureFailure(DispatchContext, Map, BigDecimal, GenericValue, GenericValue)
  processCaptureResult(DispatchContext, Map)
  refundPayment(DispatchContext, Map)
  processRefundResult(DispatchContext, Map)
  retryFailedOrderAuth(DispatchContext, Map)
  retryFailedAuths(DispatchContext, Map)
  retryFailedAuthNsfs(DispatchContext, Map)
  getCaptureTransaction(GenericValue)
  getAuthTransaction(GenericValue)
  getAuthTime(GenericValue)
  checkAuthValidity(GenericValue, String)
  savePgr(DispatchContext, GenericValue)
  savePaymentGatewayResponse(DispatchContext, Map)
  processManualCcAuth(DispatchContext, Map)
  processManualCcTx(DispatchContext, Map)
  verifyCreditCard(DispatchContext, Map)
  testProcessor(DispatchContext, Map)
  testProcessorWithCapture(DispatchContext, Map)
  testRandomAuthorize(DispatchContext, Map)
  alwaysApproveProcessor(DispatchContext, Map)
  alwaysApproveWithCapture(DispatchContext, Map)
  alwaysDeclineProcessor(DispatchContext, Map)
  alwaysNsfProcessor(DispatchContext, Map)
  alwaysBadExpireProcessor(DispatchContext, Map)
  badExpireEvenProcessor(DispatchContext, Map)
  alwaysBadCardNumberProcessor(DispatchContext, Map)
  alwaysFailProcessor(DispatchContext, Map)
  testRelease(DispatchContext, Map)
  testCapture(DispatchContext, Map)
  testCCProcessorCaptureAlwaysDecline(DispatchContext, Map)
  testCaptureWithReAuth(DispatchContext, Map)
  testRefund(DispatchContext, Map)
  testRefundFailure(DispatchContext, Map)
  ```

  

## Store Payment Settings
* 此页面用于配置OFBIZ中各种付款方式类型的付款处理设置
* 在demo数据中，可以看到为所有支付方式配置了测试服务，包括Credit Card, Electronic Funds Transfer (EFT), PayPal, WorldPay, and Gift Cards
* 删除所有“alwaysApprove*”和“test*”服务引用非常重要，因为它们将允许虚假付款通过
* 假设您想使用它们，唯一可以保留的演示配置是 PayPal 和 WorldPay 设置。 这些不使用可配置服务，因此配置更简单
* 要设置信用卡（和某些其他支付类型）处理，只需指定用于以下每个流程的服务：
  * Payment Authorization Service（授权）
  * Payment Capture Service（捕获）
  * Payment Re-Authorization Service（再授权）
  * Payment Refund Service（退款）
  * Payment Release Authorization Service（放行授权）
* OFBiz 包含的所有支付处理服务定义都在 ${ofbiz install dir}/applications/accounting/servicedef 目录中
* 可以重点以CyberSource为例进行研究：For CyberSource see the service definitions in the services_cybersource.xml file. This includes these services: cyberSourceCCAuth, cyberSourceCCCapture, cyberSourceCCRelease, cyberSourceCCRefund, and cyberSourceCCCredit

## Payment Processor Details
* 虽然支付服务和高级设置在Catalog Manager的Store部分配置，但各种支付处理服务的详细配置在文件中配置：
```
${ofbiz install dir}/applications/accounting/config/payment.properties
```
* 在上述文件中有许多针对每种支付流程服务的注释
* 如果使用任何支付卡支付处理器，请务必检查并在必要时更改遵循一下模式的payment.properties文件开头附近的属性
```
payment.general.reauth.*.days
```

## PayPal Payment Setup
* 以PayPal支付为例进行配置说明
1. Go to Accounting - Payment Gateway Config and select "PayPal Payment Gateway" from the list
2. Please fill all those fields to made working correctly to work with PayPal:
![](./images/payment_gateway_paypal.png)
![](./images/paypal_gateway_config.png)
* Business Email : Email address of your business
* Notify URL : PayPal Notify URL (example (http://yourServerName/ecommerce/control/payPalNotify)
* Return URL : PayPal Return URL (example (http://yourServerName/ecommerce/control/orderhistory)
* Cancel Return URL : PayPal Return On Cancel URL (example http://yourServerName/ecommerce/control/payPalCancel/main)
* Image URL : Image To Use On PayPal (example (http://yourServerName/images/ofbiz_logo.gif)
* Confirm Template : Thank-You / Confirm Order Template (example /order/emailconfirmation.ftl)
* Redirect URL : PayPal Redirect URL (Sandbox http://www.sandbox.paypal.com/us/cgi-bin/webscr Production https://www.paypal.com/cgi-bin/webscr)
* Confirm URL : PayPal Confirm URL (Sandbox https://www.sandbox.paypal.com/us/cgi-bin/webscr Production http://www.paypal.com/cgi-bin/webscr)

3.  Once PayPal Payment Gateway has been configurated you have to go to Catalog - Stores - select your Store - Payments tab ![](./images/store_payments_tab.png)

4.  Edit the Payment Method Type Paypal and choose as Payment Gateway Config Id "PayPal Config". 

   ![](./images/edit_paypal_method.png)

5.  As deprecated use you can alternatively change the configuration parameters into 

   ```
   ${ofbiz install dir}/applications/accounting/config/payment.properties
   ```

   * The ones that always need to be changed for use of PayPal are: 
     *  payment.paypal.business - set to an email address on your PayPal account 
     *  payment.paypal.notify - just change domain name and port to the production values you are using 
     *  payment.paypal.return - set to the URL where you want PayPal to send  customers once payment is complete, typically back to your ecommerce web site 
     *  payment.paypal.cancelReturn - set to the URL where you want PayPal to send customers when they cancel their payment 
     *  payment.paypal.image - set to the URL of the image or logo you want  PayPal to display to help customers know that the payment is being  received on your behalf 

   * The other properties beginning with "payment.paypal." can be set, but  unless you know what you are doing we recommending leaving them as-is. （ 以“payment.paypal”开头的其他属性。 可以设置，但除非您知道自己在做什么，否则我们建议您保持原样 ）

   * 除了 payment.properties 文件中的设置外，您还必须在 PayPal 网站上更改您帐户中的一项设置，以便将通知发送回 OFBiz 以验证付款 

## CyberSource Payment Setup
* 以CyberSource支付为例进行配置说明
1. Put cybsclients15.jar, cybssecurity.jar and xalan.jar from CyberSource SDK in the directory in
```
${ofbiz install dir}applications/accounting/lib/cybersource.
```
2. Change the accounting build.xml and comments to not exclude verisign sources like here :
```xml
<!- <exclude name="org/ofbiz/accounting/thirdparty/cybersource/**"/> ->
```
* 未找到build.xml，估计是版本更新了
3. Confirm that applications/accounting/build/classes/org/ofbiz/accounting/thirdparty/cybersource/IcsPaymentServices.class was built and exists
4. The installation of certificate is requested and you can follow the instructions into the CyberSource Certificate Update manual.
5. Go to Accounting - Payment Gateway Config and select "CyberSource Payment Gateway" from the list
6. Please fill all those fields to made working correctly to work with CyberSource:
7. 参见_https://cwiki.apache.org/confluence/display/OFBENDUSER/Apache+OFBiz+Business+Setup+Guide#ApacheOFBizBusinessSetupGuide-PayflowProPaymentSetup