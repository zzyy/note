Android library��ʼ������ȡContext
-------
### ����������������
����library��Ŀʱ, ������Ҫ��ȡContext����, ������Ҫ��Application�г�ʼ��, ���̱ȽϷ���, �ܷ���lib�в������ⲿ����,������ȡ��Context������;

#### ����취
��lib������һ��provider, ����provider��onCreate�����оͿ��Ի�ȡContext����, ���Ա�������, ��lib��Ŀ��ʹ��;               
ͬʱprovider�ĳ�ʼ��, ����Application��ʼ��֮��, Application��onCreate��������֮ǰ, ��ʱҲ��������ȥ��ʼ��lib��;        

��һ����������ͨ���������ActivityThread, ��ȡ mInitialApplication ����, ��Ϊϵͳ�汾�仯, �з���, ��Ҫ�Լ�������;       

