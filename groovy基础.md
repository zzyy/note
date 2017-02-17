## Groovy   
gradle����groovy;     

1. ����������������  

	    // Groovy��֧�ֶ�̬���ͣ������������ʱ����Բ�ָ�������͡�Groovy�У������������ʹ�ùؼ���def��ע��, def���Ǳ����
	    def variable1 = 1   //���Բ�ʹ�÷ֺŽ�β  
	    def varable2 = "I ama person" 
	    def  int x = 1  //��������ʱ��Ҳ����ֱ��ָ������    
	
	    //�������壺List������[]���壬����  
	    def aList = [5,'string',true] //List��[]���壬��Ԫ�ؿ������κζ��� 
	
	    //�������壺Map������[:]���壬����  
	    def aMap = ['key1':'value1','key2':true]  
	
	    //Range���͵ı��� ��beginֵ+������+endֵ��ʾ,�������aRange����1,2,3,4,5��5��ֵ
	    def aRange = 1..5   
	    //�������������һ��Ԫ�أ���  
	    def aRangeWithoutEnd = 1..<5  <==����1,2,3,4��4��Ԫ��  

2. �ַ���

    1. ������''�е������ϸ��ӦJava�е�String������$���Ž���ת��  

        	def singleQuote='I am $ dolloar'  //�������I am $ dolloar 

    2. ˫����""��������ͽű����ԵĴ����е�������ַ�����$�ŵĻ���������$���ʽ����ֵ��
    3. ��������'''xxx'''�е��ַ���֧�����⻻��

3. ���� 
	
	    //��������ʱ������������Ҳ���Բ�ָ��������  
	    String testFunction(arg1,arg2){//����ָ����������  
	      ...  
	    } 
	
	    //�����͵ĺ������壬����ʹ��def�ؼ���  
	    def  nonReturnTypeFunc(){  
	        last_line   //���һ�д����ִ�н�����Ǳ������ķ���ֵ  
	    } 
	
	    //���ָ���˺����������ͣ���ɲ��ؼ�def�ؼ��������庯��  
	    String getString(){  
	       return"I am a string"  
	    } 

		// ���巽��ʱ, ����ָ��Ĭ�ϲ���
		def foo(String p1, int p2 = 1) {  // ָ��Ĭ��p2=1
			println(p1)
			println(p2)
		}
		foo("hello")
		
		// 

2. �հ� Closure
    һ�ε����Ĵ����; 

	    //�հ���һ�δ��룬������Ҫ�û�����������..
	    def aClosure = {
	        String param1, int param2 ->  //�����ͷ�ܹؼ�����ͷǰ���ǲ������壬��ͷ�����Ǵ���  
	        println"this is code" //���Ǵ��룬���һ���Ƿ���ֵ��  
	       //Ҳ����ʹ��return����Groovy����ͨ����һ��  
	    }
	
	    // �հ�ִ��,  �հ�����.call(����)   ����  �հ�����(����) 
	    aClosure.call("this is string",100)  
	    aClosure("this is string", 100) 
	
	    //����հ�û��������Ļ�����������һ������������������ֽ�it����this���������ơ�it����հ��Ĳ�����
	    def greeting = { "Hello, $it!" }  
	    // ��ͬ�ڣ�  
	    def greeting = { it -> "Hello, $it!"} 


  - closure��Ϊ��������
    1. ֻ����һ���������Ҳ�����closure�ķ����� myMethod(myClosure) 
    2. �������ֻ����һ�����������ſ���ʡ�ԣ� myMethod myClosure 
    3. ����ʹ��������closure�� myMethod {println ��Hello World��} 
    4. �������������ķ����� myMethod(arg1, myClosure) 
    5. ��4���ƣ�����closure�������ģ� myMethod(arg1, { println ��Hello World�� }) 
    6. ������һ��������closure�������Դ�С���Ŵ��ó����� myMethod(arg1) { println ��Hello World�� }

1. ����
	
		// �����ʼ��, ����ͨ��map�ķ�ʽ, ���ݳ�ʼ����ֵ
		// �����Դ�getter��setter����,����������Ϊ final ��ʱ��Ĭ�Ͼ�û�� setter; �������е�name��age�����Դ�getter��setter
		class People{
			String name
			int age
		}

		People p1 = new People();
		People p2 = new People(name:"Luis",age: 29)//ͨ������ map �ķ�ʽ��ֵ����
	
> ÿ�д������ʡ�Էֺ�
> Groovy�к������õ�ʱ��, �������ֻ����һ�����������ſ���ʡ��


�ο�:
http://groovy-lang.org/objectorientation.html
http://kvh.io/cn/embrace-android-studio-groovy-gradle.html