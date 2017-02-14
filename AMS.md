�Լ���һ��ʼ�

### Activity������
ƽʱ����startActivity����һ��Activity, ���÷�����, ������ʲô, ActivityManagerServices����ʲô;

Activity A ����Activity B, ������˵���Է�Ϊһ�¼�������
1. ǰ�ڵĲ���У��, Ȼ��֪ͨAMS������Activity
2. AMS�յ�������Ϣ��, �ڽ���һ��У��, ͬʱ����Activity����ջ
3. AMS֪ͨActivity A��ͣ, ������onPause����;
3. AMS֪ͨActivity B���ڵĽ���ȥ����Activity, ������̲�����, ���ڴ��������(��Application)
4. ActivityThread����Activity B ������


> AMS����Activity, ��ͨ��IApplicationThread�ӿ�,��app����Ϣ;  һ���ǵ��� scheduleXxx����, Ȼ��ͨ��Handler������Ϣ, ���������� handleXxx����, Ȼ�����performXxx����, ��Ȼ��,����Instrumentation��callActivityXxx����, ������Activity��performXxx����, Ȼ��ص�����onXxx����
eg: ����Activity;
ApplicationThread#scheduleLaunchActivity -> 
ActivityThread#handleLaunchActivity -> 
ActivityThread#performLaunchActivity ->
 mInstrumentation.callActivityOnCreate -> 
activity.performCreate -> 
activity.onCreate

### AMS
1. ActivityManagerServices��ϵͳ����, ��ϵͳ������, �÷���ͻ�����; 
2. ͨ����App��, ͨ��ϵͳ�ṩ��ActivityManager�Ľӿ�ȥ����;
3. AMS��ƽʱд��AIDLһ��, CS�ܹ�, �������ActivityManagerService, ��ActiviyThread��, ͨ�������ActivityManagerProxyȥ����

![](./AMS_class.png)

- ActivityManagerNative : ��ƽʱ��AIDL���ɵ���һ��, ��һ���ڲ���ActivityManagerProxy, ͨ��Binder��Զ�̵�ActivityManagerServiceͨѶ; �����и�getDefault����, �ṩһ��IActivityManager�ĵ���, ͨ����ȥ��AMSͨѶ;
- ActivityManager : ϵͳ�����ṩ��AMS���ýӿ�; �ڲ���Ҫ��ͨ�� ActivityManagerNative.getDefault��ȡ AMS��proxy����, Ȼ��ȥ��AMSͨѶ
- ActivityService : AMS�ķ����
	1. ����ActivityStackSupervisor mStackSupervisor, ͨ����ȥ��������Activity����ջ;
	2. ����ArrayList<TaskRecord> mRecentTasks; ������������еĳ���
	2. ����ProcessMap<ProcessRecord> mProcessNames, ���������е�Application;
	3. ͬʱͨ��IApplicationThread��app����ͨѶ, ͨ�����ÿ���Activiy����������; 
	
- ApplicationThread: ������ṹ��AMS����; 
 1. ������ApplicationThread��ActivityThread��, ����app��; 
 3. ��app����AMSʱ, ���ApplicationThread��Ӧ��binder���ݹ�ȥ
 2. AMSͨ��ApplicationThreadProxy��Binder���õ�app, �Ӷ�����app����������;
 
 -----
��ͼ, ��ûŪ��, ��ʱ������������
https://www.processon.com/view/link/58a3105ee4b006dc8c0ce1f0