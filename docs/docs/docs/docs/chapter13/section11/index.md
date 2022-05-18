# typeScript类型-Partial、Pick、Omit

## partial 
```
//Partial 类型的定义  
  
/** 
 * Make all properties in T optional 
 */  
type Partial<T> = {  
    [P in keyof T]?: T[P];  
};  
  
//使用Partial  
  
interface IUser {  
  name: string  
  age: number  
  department: string  
}  
  
type optional = Partial<IUser>  
  
// optional的结果如下  
type optional = {  
    name?: string | undefined;  
    age?: number | undefined;  
    department?: string | undefined;  
}  
```
## omit

**pick**和它相反.
```
//Omit<K,T>类型让我们可以从另一个对象类型中剔除某些属性，并创建一个新的对象类型：  
//K：是对象类型名称，T：是剔除K类型中的属性名称  
  
type  UserProps =  {  
  name?:string;  
  age?:number;  
  sex?:string;  
}  
// 但是我不希望有sex这个属性我就可以这么写  
type NewUserProps =  Omit<UserProps,'sex'>   
// 等价于  
type  NewUserProps =  {  
  name?:string;  
  age?:number;  
}  
```