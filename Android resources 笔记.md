1. 一个进程有一个ResourcesManager类, 单例; 一个ResourcesManager类管理多个Resources;
2. ResourcesManager中, 由`mActiveResources`的map, 保存多个`ResourcesKey` 和 `Resources`的键值对, (不同分辨率, 语言); 由`getTopLevelResources`获取对应的Resources
3. ContextImpl对象, 包含一个`mResources`的变量, context对象的`getResources`即返回此变量, 而`mRseources`对象由 `getTopLevelResources`方法获取
4. Resources对象包含 AssetManager 对象
5. app不走正常的初始化,没法获得AssetManager, 但是可以通过反射创建, AssetManager的私有方法 `addAssetPath` 接受一个dex文件的路径, 可以生成对于的AssetManager

  ```java
  AssetManager assetManager = AssetManager.class.newInstance();
  Method addAssetPathMethod = assetManager.getClass().getMethod("addAssetPath", String.class);
  addAssetPathMethod.invoke(assetManager,  apkPath );
  ```

6. 根据原apk的`context`对象, 可以创建一个新的Resources对象

  ```java
  Resources superRes = context.getResources();
  Resources apkResource = new Resources(assetManager,superRes.getDisplayMetrics(),superRes.getConfiguration());
  ```

7. 每个资源属性的定义, 由 packageName, TypeName, entryName 构成, 可以由`getResourceName(resId)`获得, "package:type/entry", 也可分别获得 `getResourcePackageName` `getResourceTypeName` `getResourceEntryName`

8. 由 `Resources.getIdentifier` 方法, 根据 entryName, typeName, package, 可获取对应资源在Resources对象中唯一的id;

  ```
  int resId = mResources.getIdentifier(resName, "drawable", packageName);
  ```

9. 所有的资源,需要通过一个唯一的id来访问,格式为 0xPPTTNNNN

  - **PP** - the resources package id(00-02: system, 7f: application, 03-7e: reserved)
  - **TT** - the resource type id (eg. 'layout','string','attr' and etc.)
  - **NNNN** - the resource entry id

10. 系统资源的初始化   
    1. ZygoteInit.preload() -> ZygoteInit.preloadResources() -> Resources.getSystem() -> mResources.startPreloading()
