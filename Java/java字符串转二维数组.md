java字符串转二维数组，二维数组转List <List <String >>,List<List<String>>转二维数组
使用的fastjson

  		String str1 = "[[\"1\",\"2\",\"3\"],[\"12\",\"3\",\"4\"]]";
  	    String str2 = "[[1,2,3],[12,3,4]]";
  	    String[][] parse1 = JSON.parseObject(str1, String[][].class);
  	    String[][] parse2 = JSON.parseObject(str2, String[][].class);
  	
  	    List<List<String>> lists1 = JSON.parseObject(JSON.toJSONString(parse1),
  	            new TypeReference<List<List<String>>>() {
  	            });
  	    List<List<String>> lists2 = JSON.parseObject(JSON.toJSONString(parse1),
  	            new TypeReference<List<List<String>>>() {
  	            });
  	    String[][] parse3 = JSON.parseObject(JSON.toJSONString(lists1), String[][].class);
  	    String[][] parse4 = JSON.parseObject(JSON.toJSONString(lists2), String[][].class);
  	
  	    System.out.println(JSON.toJSONString(parse1));
  	    System.out.println(JSON.toJSONString(parse2));
  	    System.out.println(JSON.toJSONString(lists1));
  	    System.out.println(JSON.toJSONString(lists2));
  	    System.out.println(JSON.toJSONString(parse3));
  	    System.out.println(JSON.toJSONString(parse4));