一个综合的Map测试题
------

```
/*
 * 练习：
 * "sdfgzxcvasdfxcvdf"获取该字符串中字符出现的次数。
 * 希望打印结果：
 * a(1)c(2)..
 * 
 * 通过结果发现，每一个字母都有对应的映射次数
 * 说明字母和次数之间都有映射关系。
 * 注意了：当发现有映射关系时，可以选择map集合。
 * 因为map集合中存放的就是映射关系。
 * 
 * 什么时候使用map集合呢？
 * 当数据之间存在这种映射关系时，就要先想到map集合。
 * 
 * 思路：
 * 1.将字符串转换成字符数组，因为要对每一个字母进行操作。
 * 2.定义一个map集合，因为打印的字母有顺序，所以使用TreeMap
 * 3.遍历字符数组：
 *   将每一个字母作为键去查map集合，如果返回null，将该字母和1存入map集合中，如果返回值不是null，
 *   说明该字母在map集合中存在并有对应次数。
 *   那么就获取该次数，并进行自增，然后将该字母和自增后的次数存入到map集合中，覆盖掉原来键所对应的值。
 * 4.将map中的数据变成指定的形式返回。
 * 
 * 
 */
public class MapTest3 {
	public static void main(String[] args) {
		String s = "abcadefdd";
		System.out.println(charCount(s));
	}

	public static String charCount(String str) {
		char[] chs = str.toCharArray();
		
		TreeMap<Character, Integer> tm = new TreeMap<Character, Integer>();
		
		for (int i = 0; i < chs.length; i++) {
			Integer value = tm.get(chs[i]);
			
			tm.put(chs[i],value==null?1:++value);
			
		/*	if (value == null) {
				tm.put(chs[i], 1);
			} else {
				value = value + 1;
				tm.put(chs[i], value);
			}*/
		}
		// System.out.println(tm);
		
		Set<Map.Entry<Character, Integer>> entrySet = tm.entrySet();
		
		Iterator<Map.Entry<Character, Integer>> it = entrySet.iterator();
		
		StringBuilder sb = new StringBuilder();
		
		while (it.hasNext()) {
			Map.Entry<Character, Integer> me = it.next();
			Character key = me.getKey();
			Integer value = me.getValue();
			sb.append(key + "(" + value + ")");
		}
		
		return sb.toString();

	}
}
```