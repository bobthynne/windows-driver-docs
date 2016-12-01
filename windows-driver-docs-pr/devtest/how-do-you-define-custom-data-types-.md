---
title: How do you define custom data types
description: How do you define custom data types
ms.assetid: 849f83ae-5fe7-447b-9d02-3d980757bf7d
---

# How do you define custom data types?


Event Tracing for Windows (ETW) defines several simple and complex types for use in the tracing functions. These types are declared in the Defaultwpp.ini file. However, you can create your own custom data types.

You use a custom data type when you want to declare variables and use meaningful terms--instead of integers--to describe the value of the variables.

For example, the *DiskState* variable contains the state of the disk. The following are values of *DiskState*:

```
DiskOffline =0
DiskOnline = 1
DiskFailed = 2
DiskStalled = 3
```

Instead of reading "DiskState=2" in a trace message, and then having to look up the meaning of **2**, you can define a custom type called *DiskState*, to get a trace message that says "DiskState is Failed."

### <span id="creating_a_custom_data_type"></span><span id="CREATING_A_CUSTOM_DATA_TYPE"></span>Creating a Custom Data Type

To create a custom data type, complete the following steps:

1.  Create a local configuration file that has the .ini file name extension, such as localwpp.ini. You cannot add a custom type to a header or source file.

2.  Use the TYPEMACRO constant to define the custom data type.

3.  Identify the configuration data in your sources or header file.

4.  Add the **-ini** parameter to the RUN\_WPP macro in your source file.

5.  Use the custom data type in trace messages.

### <span id="defining_a_typemacro_constant"></span><span id="DEFINING_A_TYPEMACRO_CONSTANT"></span>Defining a TYPEMACRO constant

Define a TYPEMACRO constant that has the following format. The values are defined as a list of strings.

```
TYPEMACRO(Type,{ItemListLong | ItemListShort | ItemListByteShort | ItemListByteLong},(Value1,Value2...));
```

where:

<span id="ItemListShort"></span><span id="itemlistshort"></span><span id="ITEMLISTSHORT"></span>**ItemListShort**  
Signed 16-bit integer.

<span id="ItemListLong"></span><span id="itemlistlong"></span><span id="ITEMLISTLONG"></span>**ItemListLong**  
Signed or unsigned 32-bit integer.

<span id="ItemSetByteShort"></span><span id="itemsetbyteshort"></span><span id="ITEMSETBYTESHORT"></span>**ItemSetByteShort**  
Signed 16-bit bit value.

<span id="ItemSetByteLong"></span><span id="itemsetbytelong"></span><span id="ITEMSETBYTELONG"></span>**ItemSetByteLong**  
Signed or unsigned 32-bit bit value.

For example:

```
TYPEMACRO(DiskState,ItemListLong(DiskOffline,DiskOnline,DiskFailed,DiskStalled));
```

### <span id="identifying_the_configuration_data"></span><span id="IDENTIFYING_THE_CONFIGURATION_DATA"></span>Identifying the Configuration Data

If you added the custom data type to a file that has other code, such as a source file or a header file, then use the **begin\_wpp config** and **end\_wpp** statements to identify the configuration data in the file. For example:

```
// begin_wpp config
    //TYPEMACRO(DiskState,ItemListLong(DiskOffline,DiskOnline,DiskFailed,DiskStalled));
// end_wpp
```

If you added the custom data type to a local configuration file, then the **begin\_wpp config** and **end\_wpp** statements are not needed.

### <span id="add_the__ini_parameter"></span><span id="ADD_THE__INI_PARAMETER"></span>Add the -ini parameter

When you create a local configuration file for a custom type, you need to add the **-ini** parameter to the RUN\_WPP statement that invokes the WPP preprocessor.

The **-ini** parameter directs ETW to search for configuration data in configuration files (.ini), in addition to using Defaultwpp.ini. For example:

```
RUN_WPP -km -ini:localwpp.ini
```

**Note**  You must not specify the **-km** switch in the RUN\_WPP directive for user-mode applications or dynamic-link libraries (DLLs).

 

### <span id="using_the_custom_data_type"></span><span id="USING_THE_CUSTOM_DATA_TYPE"></span>Using the Custom Data Type

After you have defined a custom data type, you can use it in trace messages. Precede the type name with a percent sign (**%**) and surround it with exclamation marks (!). For example:

```
DoTraceMessage(INFO,"Disk State is %!diskstate!",DiskState); 
```

The resulting trace message uses the constant that you defined to represent the value:

```
DiskState is Offline.
```

 

 

[Send comments about this topic to Microsoft](mailto:wsddocfb@microsoft.com?subject=Documentation%20feedback%20[devtest\devtest]:%20How%20do%20you%20define%20custom%20data%20types?%20%20RELEASE:%20%2811/17/2016%29&body=%0A%0APRIVACY%20STATEMENT%0A%0AWe%20use%20your%20feedback%20to%20improve%20the%20documentation.%20We%20don't%20use%20your%20email%20address%20for%20any%20other%20purpose,%20and%20we'll%20remove%20your%20email%20address%20from%20our%20system%20after%20the%20issue%20that%20you're%20reporting%20is%20fixed.%20While%20we're%20working%20to%20fix%20this%20issue,%20we%20might%20send%20you%20an%20email%20message%20to%20ask%20for%20more%20info.%20Later,%20we%20might%20also%20send%20you%20an%20email%20message%20to%20let%20you%20know%20that%20we've%20addressed%20your%20feedback.%0A%0AFor%20more%20info%20about%20Microsoft's%20privacy%20policy,%20see%20http://privacy.microsoft.com/default.aspx. "Send comments about this topic to Microsoft")



