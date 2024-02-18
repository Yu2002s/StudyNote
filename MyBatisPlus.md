##### 更新数据

```java
// 更新的条件以及字段
UpdateWrapper<User> wrapper = new UpdateWrapper<>();
wrapper.eq("id", 6).set("age", 23); 
// 执行更新操作
int result = this.userMapper.update(null, wrapper); 
System.out.println("result = " + result);
```

##### 删除数据

```java
Map<String, Object> columnMap = new HashMap<>();
columnMap.put("age",20);
columnMap.put("name","张三");
//将columnMap中的元素设置为删除的条件，多个之间为and关系
int result = this.userMapper.deleteByMap(columnMap);
System.out.println("result = " + result);

User user = new User();
user.setAge(20);
user.setName("张三");
//将实体对象进行包装，包装为操作条件
QueryWrapper<User> wrapper = new QueryWrapper<>(user);
int result = this.userMapper.delete(wrapper);
System.out.println("result = " + result);
```

