> 只能向父controller传递event与data   
用$emit，$scope.$emit("参数名称",参数值);

> 只能向子controller传递event与data  
$scope.$broadcast("参数名称",参数值);  

> $on用于接收event与data
```
$scope.$on("参数名称",function(e,data){
   console.log(data);
});
```