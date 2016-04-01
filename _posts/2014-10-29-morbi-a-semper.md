---
layout: post
title:  "使用runtime将model转为data或者dictionary"
date:   2016-03-31 14:36:23
---
 在实现上，属性的枚举是通过定义在<objc/runtime.h>中的class_copyPropertyList方法实现。其次，属性值的获取是通过KVC中的valueForKey方法，这个方法同时可以将非对象类型（如BOOL, int等）转换成NSNumber。

接着就是对数组，字典和对象类型值的嵌套处理，所有值就可以获取出来了。

至于JSON，如果正确获取了NSDictionary后，直接使用iOS 5后的NSJSONSerialization类型的dataWithJSONObject方法就可以返回包含JSON字符串的NSData对象了。（使用继承或者分类实现）


	#import
 	<Foundation/Foundation.h>

	@interface
 	PrintObject : NSObject

	//通过对象返回一个NSDictionary，键是属性名称，值是属性值。

	+(NSDictionary*)getObjectData:(id)obj;

	//将getObjectData方法返回的NSDictionary转化成JSON

	+(NSData*)getJSON:(id)obj options:(NSJSONWritingOptions)options error:(NSError**)error;

	//直接通过NSLog输出getObjectData方法返回的NSDictionary

	+(void)print:(id)obj;

	@end


.m文件实现过程


	#import"PrintObject.h"

	#import<objc/runtime.h>

  

	@implementation	PrintObject

  

	+(NSDictionary*)getObjectData:(id)obj

	{

    NSMutableDictionary*dic = [NSMutableDictionary dictionary];

    unsigned int propsCount;

    objc_property_t *props = class_copyPropertyList([obj class], &propsCount);

    for(int i = 0;i < propsCount; i++)

    {

        objc_property_t prop = props[i];

        

        NSString*propName = [NSString stringWithUTF8String:property_getName(prop)];

        id value = [obj valueForKey:propName];

        if(value == nil)

        {

            value = [NSNull null];

        }

        else

        {

            value = [self getObjectInternal:value];

        }

        [dic setObject:value forKey:propName];

    }

    return dic;

	}

  

	+ (void)print:(id)obj

	{

    NSLog(@"%@",[self getObjectData:obj]);

	}

  

  

	+(NSData*)getJSON:(id)obj options:(NSJSONWritingOptions)options error:(NSError**)error

	{

    return [NSJSONSerialization dataWithJSONObject:[self getObjectData:obj] options:options error:error];

	}

  

	+ (id)getObjectInternal:(id)obj

	{

    if([obj isKindOfClass:[NSString class]]

       ||[obj isKindOfClass:[NSNumber class]]

       ||[obj isKindOfClass:[NSNull class]])

    {

        return obj;

    }

    

    if([obj isKindOfClass:[NSArray class]])

    {

        NSArray *objarr = obj;

        NSMutableArray *arr = [NSMutableArray arrayWithCapacity:objarr.count];

        for(int i = 0;i < objarr.count; i++)

        {

            [arr setObject:[self getObjectInternal:[objarr objectAtIndex:i]] atIndexedSubscript:i];

        }      

        return arr;

    }

    

    if([obj isKindOfClass:[NSDictionary class]])

    {

        NSDictionary *objdic = obj;

        NSMutableDictionary *dic = [NSMutableDictionary dictionaryWithCapacity:[objdic count]];

        for(NSString *key in objdic.allKeys)

        {

            [dic setObject:[self getObjectInternal:[objdic objectForKey:key]] forKey:key];

        }     

        return dic;

    } 

    return [self getObjectData:obj];

	}

  

	@end
