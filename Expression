using System;
using System.Linq.Expressions;


//表达式树的基本元件
Expression<Func<int,bool>> cheAgeExpression = age => age >= 18;

var binaryBody = (BinartExpression)checkAgeExpr.Body;
var left = (ParameterExpression)binaryBody.Left;
var right = (ConstantExpression)binaryBody.Right;

Func<int,bool> compiledFunc = checkAgeExpr.Compile();
Console.WriteLine(compiledFunc(20));

//简单的应用一
public static Expression<Func<T,bool>> BuildPropertyEquals<T>(string propertyName, object value)
{
	var parameter = Expression.Parameter(typeof(T),"x");
	var property = Expression.Property(parameter,propertyName);
	var constant = Expression.Constant(value);
	var equals = Expression.Equal(property,constant);

	return Expression.Lambda<Func<T,bool>>(equals,parameter);
}
//基于输入属性名称的属性比较表达式
var expr = BuildPropertyEquals<User>("Name","Joseph");
var user = dbContext.Users.FirstOrDefault(expr);

//简单的应用二-Mapper
public static Func<TSource,TTarget> CreateMapper<TSource,TTarget>(){
	var sourceParam = Expression.Parameter(typeof(TSource),"source");
	var bindings = typeof(TTarget).GetProperties()
		.Select(targetProp =>{
			var sourceProp = typeof(TSource).GetProperty(targetProp.Name);
			return Expression.Bind(targetProp,Expression.Property(sourceParam,sourceProp));
		})

	var body = Expression.MemberInit(Expression.New(typeof(TTarget)),bindings);

	return Expression.Lambda<Func<TSource,TTarget>>(body,sourceParam).Compile();
}
//User与UserDto都包含Name属性
var mapper = CreateMapper<User,UserDto>();
var dto = mapper(userEntity);

//简单的应用三
//构建以下逻辑： int temp = x + 1;return temp * 2;
var x = Expression.Parameter(typeof(int),'x');
var temp = Expression.Parameter(typeof(int),'temp');

var block = Expression.Block(
	new[] {temp},
	Expression.Assign(temp.Expression.Add(x,Expression.Constant(1))),
	Expression.Mutiply(temp,Expression.Constant(2))
);

var func = Expression.Lambda<Func<int,int>>(block,x).Compile();
Console.WriteLine(func(5));

//简单的引用四-ExpressionVisitor
public class AgeSecurityVisitor : ExpressionVisitor{
	protected override Expression VisitBinary(BinaryExpression node){
		if(node.Left is MemberExpression m && m.Member.Name == "Age")
		{
			return Expression.MakeBinary(node.NodeType,node.Left,Expression.Constant(18))
		}
		return base.VisitBinary(node);
	}
}

Expression<Func<User,bool>> expr = u => u.Age > 10 && u.Name != "Dio";
var visitor = new AgeSecurityVisitor();
var newExor = (Expression<Func<user,bool>>)visitor.Visit(expr);
//表达式变为了u => u.Age > 18 && u.Name != "Dio"