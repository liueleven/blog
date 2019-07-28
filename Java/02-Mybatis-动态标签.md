### MyBatis常用动态Sql拼接

#### if

判断某个字段属性是否符合条件

```xml
select * from tb_user where age = #{age} 
<if test="username != null and username != ''" >
     // do something 
 </if>
```

#### where

可以去除掉后面多余的OR或AND

```xml
select * from tb_user where age = #{age} 
<where>
  <if test="username != null and username != ''" >
       and username = #{username}
  </if>
</where>
```

#### choose-when-otherwise

类似于switch-case,when都没有匹配就执行otherwise

```xml
select * from tb_user 
<where>
	 <choose>
     <when test="username != null ">
			 AND	username LIKE CONCAT(CONCAT('%', #{username}),'%')
     </when >
     <when test="sex != null and sex != '' ">
       AND sex = #{sex}
     </when >
     <otherwise>
       id = #{id}
     </otherwise>
  </choose>
</where>

```

#### foreach

循环遍历list，array等

```xml
select * from tb_user where status in
<foreach collection="list" item="orderId"  open="(" separator="," close=")">
  #{orderId}
</foreach>
```

#### set

更新，会把末尾多余的逗号除去

```xml
update tb_user
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
    </set>
where id = #{id}
```



### 参考

- [官方文档](http://www.mybatis.org/mybatis-3/zh/dynamic-sql.html)