### 授权子账号拥有CLB的只读权限

企业帐号CompanyExample（ownerUin为12345678）下有一个子账号Developer，该子账号需要拥有对企业帐号CompanyExample的CLB服务的查看权限，但子账号无法创建、更新或删除它们。

方案A：

企业帐号CompanyExample直接将预设策略QcloudCLBReadOnlyAccess授权给子账号Developer。授权方式请参考[授权管理](/document/product/378/8961)。

方案B：

step1：通过策略语法方式创建以下策略
```
 {
    "version": "2.0",
    "statement":
     {
         "effect": "allow",
         "action": "clb:Describe*",
         "resource": "*"
     }
}
```
step2：将该策略授权给子账号。授权方式请参考[授权管理](/document/product/378/8961)。
