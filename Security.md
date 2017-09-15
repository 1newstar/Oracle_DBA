# Security

Oracle Data Redaction
  这个特性，可以防止数据列(如信用卡号码、美国社会安全号码和其他敏感或受规管的数据)被显示，避免敏感数据泄露。这个特性不会修改存储在数据库中的真实数据，当授权用户访问时，真实的数据才会显示。与这个功能相关的包是DBMS_REDACT
  
Support for Secure Hash Algorithm SHA-2 in Oracle Database
  支持SHA-2算法，扩展支持SHA-2算法包括PL/SQL包dbms_crypto   

Auditing Enabled By Default
  新的统一审计架构可用于在不改版初始化参数的情况下启用。无需停机时间，提供了灵活性和易管理性。审计是默认开启的，可通过存储在表空间SYSAUX中的视图AUDSYS.UNIFIED_AUDIT_TRAIL查看。

Enhanced Security of Audit Data
  审计数据存在只读的表中，只有这个AUDIT_ADMIN角色的用户才能去管理和维护

Separation of Duty for Audit Administration
  审计角色分开，AUDIT_ADMIN有创建和和启用新审计策略，指定审计保留策略。AUDIT_VIEWER提供了审计人员和安全管理人员在新的统一的审计下的审计数据的能力。

Separation of Duty for Database Administration
  增加了SYSBACKUP,SYSDG,SYSKM等权限，可以不必把SYSDBA权限授予一般用户
