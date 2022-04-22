# Payment Gateway
## PaymentGatewayConfig 

| 字段名                         | 描述                                               | 示例               |
| ------------------------------ | -------------------------------------------------- | ------------------ |
| PAYMENT_GATEWAY_CONFIG_ID      | 主键，支付网关配置id                               | CYBERSOURCE_CONFIG |
| PAYMENT_GATEWAY_CONFIG_TYPE_ID | 外键（PAYMENT_GATEWAY_CONFIG），支付网关配置类型id | PAY_GATWY_CYBERSRC |
| DESCRIPTION                    | 配置描述                                           | CyberSource Config |

## PaymentGatewayConfigType

| 字段名                         | 描述                     | 示例               |
| ------------------------------ | ------------------------ | ------------------ |
| PAYMENT_GATEWAY_CONFIG_TYPE_ID | 主键，支付网关配置类型id | PAY_GATWY_CYBERSRC |
| DESCRIPTION                    | 配置描述                 | CyberSource Config |

