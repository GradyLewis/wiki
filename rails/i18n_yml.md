# i18n locales.yml 数据库本地化格式 activerecord
```
# 数据库对象中文翻译
cn:
  activerecord:
    models:
      admin_user: '管理员'
      face_score: '颜值得分'
      face_help: '好友助力'
      face_set: '活动获奖规则'
    attributes:
      admin_user:
        email: '邮件地址'
        current_sign_in_at: '当前登录时间'
        last_sign_in_at: '上次登录时间'
        sign_in_count: '总登录次数'
      face_score:
        person_info_id: '用户'
        score: '得分'
```
可拆分成多个节点，放在多个yml文件中，以 cn 开头，例如：
```
cn:
  errors:
    messages:
      already_confirmed: "已经确认，请重新登录"
      confirmation_period_expired: "确认需要在%{period}内, 请重新提交请求"
      expired: "已过期, 请重新提交请求"
      not_found: "未找到"
      not_locked: "已锁定"

```