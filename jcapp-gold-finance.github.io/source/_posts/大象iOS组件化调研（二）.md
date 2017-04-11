# 大象iOS组件化调研（二）

## CocoaPods配合git做代码版本管理

> 作者：华文杰

1. 先在公司服务器上创建一个git库

  然后下载下来，

2. 在本地git库中创建podspec文件

   ```objective-c
   pod spec create JCLogin 
   ```

3. 打开JCLogin.podspec，然后将git地址改为本地git库的地址

   ```objective-c
   s.source       = { :git => "本地git库地址" }
   s.source_files  = '**/*.{h,m}'
   ```

   这里如果有.a等第三方静态库集成的时候可能会遇到找不到.a文件或者链接错误，从stackoverflow里，找到配置library的写法：

   ```objective-c
   s.source_files = 'StaticLib/Headers/*.h'
   s.preserve_paths = 'StaticLib/libYourLibrary.a'
   s.library = 'YourLibrary'
   s.xcconfig = { 'LIBRARY_SEARCH_PATHS' => '$(PODS_ROOT)/ProjectFolder/LibraryFolder' }    

   s.dependency = 'AFNetworking'
   ```

   ​

4. 如同使用其他pod一样

   ```objective-c
   pod 'JCLogin', :path => '本地库地址'
   ```

   ​

