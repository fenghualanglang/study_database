### 2.2.2 key命令基本操作
<table border="1" align="center" bordercolor="#4A766E" cellpadding="0" cellspacing="0">
      <caption><strong>常用操作命令如下</strong></caption>
    <tr>
        <th>序号</th>
        <th>命令</th>
        <th>描述</th>
    </tr>
    <tr>
        <td>1</td>
        <td>keys pattern</td>
        <td>查找键，参数⽀持正则表达式</td>
    </tr>
    <tr>
        <td>2</td>
        <td>exists key </td>
        <td>判断键是否存在，如果存在返回1，不存在返回0</td>
    </tr>
    <tr>
        <td>3</td>
        <td>type key</td>
        <td>查看键对应的value的类型</td>
    </tr>
    <tr>
        <td>4</td>
        <td>del key1 key2 ...</td>
        <td>删除键及对应的值</td>
    </tr>
    <tr>
        <td>5</td>
        <td>expire key seconds</td>
        <td>设置过期时间，以秒为单位， 有指定过期时间则⼀直存在，直到使⽤DEL移除</td>
    </tr>
</table>

```
  查看所有键 keys *
  查看以a开头的键  keys zhang*
  查看key为张三键是否存在 exists zhangsan
  查看key为redis类型  type key
  删除某键 del key1 key2
  设立过期时间 expire zhangsan 20
```
