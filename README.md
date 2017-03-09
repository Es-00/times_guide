# times_guide
**Times系统说明文档-模型篇**
### 预测模型
```ruby
def self.predict(key)
```
输入的key包括星期几以及教室范围

例：“星期一 J3-205”
```ruby
  dict3={"一"=>"mon","二"=>"tue","三"=>"wed","四"=>"thr","五"=>"fri"}
  if (key=~/[一二三四五]/).nil?
    return nil
  else
```
如果输入中不包括”一二三四五“这五个字符中的任意一个则返回nil
```ruby
    keyarr=key.split(" ")
    for keyword in keyarr
      unless keyword.include? "星期"
        key2=keyword
      end
    end
```
keyarr是把key以” “（即空格）分开成若干部分作为元素的数组，
再把不包含星期的那个元素赋值给key2

也就是说，key2是教室范围
```ruby
    hashes={}
    @rooms=[]
    @strs=[]
```
定义一个散列和两个数组，为后面做准备
```ruby
    if key2.nil?
      key2=""
    end
```
如果key2为空，即没有输入教室范围，则定义key2为一个没有内容的字符串

在之后的搜索中，既然key2没有内容，就会匹配所有教室
```ruby    
    Room.all.each do |r|
      if r.class_id.include? key2
        target=eval('r.'+dict3[key[key=~/["一二三四五"]/]])
        if target.nil?
          target=[""]
        else
          target=target.split("")
        end
        array=["1","2","3","4","5","6"]
        for letter in target
          if letter!=""
            array.delete(letter)
          end
        end
        str=""
        for a in array
          str<<a<<'、'
        end
        str.chop!
        hashes.merge!({r=>str})
      end
    end
    hashes=hashes.sort_by do |k,v|
      v.length
    end
    for hash in hashes
      @rooms<<hash[0]
      if hash[1]==""
        @strs<<"全天满课"
      elsif hash[1]=="1、2、3、4、5、6"
        @strs<<"全天空闲"
      else
        str='第'<<hash[1]<<'节空闲'
        @strs<<str
      end
    end
    @strs.reverse!
    return @rooms,@strs
  end
end
```
