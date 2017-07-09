---
title: Java Byte Code & ASM
date: 2017-07-05 21:47:15
tags: Java
---

# What is ASM
[ASM](http://asm.ow2.org/) is an all purpose Java bytecode manipulation and analysis framework. It can be used to modify existing classes or dynamically generate classes, directly in binary form. Provided common transformations and analysis algorithms allow to easily assemble custom complex transformations and code analysis tools.


# ASM simple exammple
Let's see what ASM can do via a simple example:
{% codeblock lang:java %}
package asmexample;

public class Target {

    public static final int version = 3;
    private int code;

    public Target(int code) {
        this.code = code;
    }

    public int getCode() {
        return code;
    }

}
{% endcodeblock %}

{% codeblock lang:java %}
package asmexample;

import org.objectweb.asm.ClassVisitor;
import org.objectweb.asm.FieldVisitor;
import org.objectweb.asm.MethodVisitor;
import org.objectweb.asm.Opcodes;

public class ClassPrinter extends ClassVisitor {

    public ClassPrinter() {
        super(Opcodes.ASM5);
    }

    @Override
    public void visit(int version, int access, String name, String signature, String superName, String[] interfaces) {
        System.out.println("----------------------------------------------------------");
        System.out.println("Class Major Version: " + version);
        System.out.println("----------------------------------------------------------");
        System.out.println(name + " extends " + superName + " {");
        super.visit(version, access, name, signature, superName, interfaces);
    }

    @Override
    public MethodVisitor visitMethod(int access, String name, String desc, String signature, String[] exceptions) {
        System.out.println("\t" + name + " " + desc);
        return super.visitMethod(access, name, desc, signature, exceptions);
    }

    @Override
    public FieldVisitor visitField(int access, String name, String desc, String signature, Object value) {
        System.out.println("\t" + desc + " " + name + " = " + value);
        return super.visitField(access, name, desc, signature, value);
    }

    @Override
    public void visitEnd() {
        System.out.println("}");
        super.visitEnd();
    }
}
{% endcodeblock %}

{% codeblock lang:java %}
package asmexample;

import org.objectweb.asm.*;
import java.io.IOException;

public class ASMHelloWorld {
    public static void main(String[] args) throws IOException {
        ClassReader cr = new ClassReader("asmexample.Target");
        cr.accept(new ClassPrinter(), 0);
    }
}
{% endcodeblock %}

{% codeblock %}
----------------------------------------------------------
Class Major Version: 51
----------------------------------------------------------
asmexample/Target extends java/lang/Object {
    I version = 3
    I code = null
    <init> (I)V
    getCode ()I
    <clinit> ()V
}
{% endcodeblock %}

Please refer to [Type descriptors](#Type-descriptors) and [Method descriptors](#Method-descriptors) for the explanation of the output.

# Structure of compiled Java class
A compiled class retains the structural information and almost all the symbols from the source code. 
<table> <tr> <td colspan="2">Modifiers, name, super class, interfaces</td> </tr> <tr> <td colspan="2">Constant pool: numeric, string and type constants</td> </tr> <tr> <td colspan="2">Source file name (optional)</td> </tr> <tr> <td colspan="2">Enclosing class reference</td> </tr> <tr> <td colspan="2">Annotation<sup>*</sup></td> </tr> <tr> <td colspan="2">Attribute<sup>*</sup></td> </tr> <tr> <td>Inner class<sup>*</sup></td> <td>Name</td> </tr> <tr> <td rowspan="3">Field<sup>*</sup></td> <td>Modifiers, name, type</td> </tr> <tr> <td>Annotation<sup>*</sup></td> </tr> <tr> <td>Attribute<sup>*</sup></td> </tr> <tr> <td rowspan="4">Method<sup>*</sup></td> <td>Modifiers, name, type</td> </tr> <tr> <td>Annotation<sup>*</sup></td> </tr> <tr> <td>Attribute<sup>*</sup></td> </tr> <tr> <td>Compiled code</td> </tr> </table>

## Internal names
Types like the super class of a class, the interfaces implemented by a class are represented in compiled class with <i>internal names</i>. The internal name of a class is just the fully qualified name of this class, where dots are replaced with slashes. For example the internal name of <b>String</b> is <b>java/lang/String</b>.

## Type descriptors
Internal names are used only for types that are constrained to be class or interface t ypes. Others, such as field types, are represented in compiled java classes with <i>type descriptors</i>.

| Java type  | Type descriptor      |
|------------|:---------------------|
| boolean    | z                    |
| char       | C                    |
| byte       | B                    |
| short      | S                    |
| int        | I                    |
| float      | F                    |
| long       | J                    |
| double     | D                    |
| Object     | [Ljava/lang/Object;  |
| int[]      | [I                   |
| Object[][] | [[Ljava/lang/Object; |
| Void       | V                    |

## Method descriptors
A method descriptor is a list of type descriptors that describe the parameter types and the return type of a method, in a single string.

| Method declaration in source file | Method descriptor              |
|-----------------------------------|:-------------------------------|
| void m(int i, float f)            | (IF)V                          |
| int m(Object o)                   | (Ljava/lang/Object;)I          |
| int[] m(int i, String s)          | (ILjava/lang/String;)[I        |
| Object m(int[] i)                 | ([I)Ljava/lang/Object;         |
| ClassName() <sup>[1]</sup>        | <init>()V                      |
| ClassName(int i) <sup>[2]</sup>   | <init>(I)V                     |

[1] [2]: These are constructors. <init> represents constructors, whereas <clinit> represents static initialization block.

# Generating a class
ASM supports generating classes dynamically as well. The only required component to generate a class is the <b>ClassWriter</b> component. Let's take an example to illustrate this:

{% codeblock lang:java %}
package asmexample;

import org.objectweb.asm.ClassWriter;
import org.objectweb.asm.Opcodes;

public class ClassMaker {
    public byte[] makeAnInterface() {
        ClassWriter cw = new ClassWriter(0);
        cw.visit(Opcodes.V1_8
                , Opcodes.ACC_PUBLIC + Opcodes.ACC_ABSTRACT + Opcodes.ACC_INTERFACE
                , "asmexample/Comparable", null, "java/lang/Object", new String[]{"asmexample/Measurable"}
        );
        cw.visitField(Opcodes.ACC_PUBLIC + Opcodes.ACC_FINAL + Opcodes.ACC_STATIC, "LESS", "I",
                null, new Integer(-1)).visitEnd();
        cw.visitField(Opcodes.ACC_PUBLIC + Opcodes.ACC_FINAL + Opcodes.ACC_STATIC, "EQUAL", "I",
                null, new Integer(0)).visitEnd();
        cw.visitField(Opcodes.ACC_PUBLIC + Opcodes.ACC_FINAL + Opcodes.ACC_STATIC, "GREATER", "I",
                null, new Integer(1)).visitEnd();
        cw.visitMethod(Opcodes.ACC_PUBLIC + Opcodes.ACC_ABSTRACT, "compareTo",
                "(Ljava/lang/Object;)I", null, null).visitEnd();
        cw.visitEnd();
        return cw.toByteArray();
    }
}
{% endcodeblock %}

{% codeblock lang:java %}
package asmexample;

import org.objectweb.asm.ClassReader;

public class WriteAnInterface {
    public static void main(String[] args) {
        ClassReader cr = new ClassReader(new ClassMaker().makeAnInterface());
        cr.accept(new ClassPrinter(), 0);
    }
}
{% endcodeblock %}

We're reusing the <b>ClassPrinter</b> to print the generated interface:

{% codeblock lang:java %}
----------------------------------------------------------
Class Major Version: 52
----------------------------------------------------------
asmexample/Comparable extends java/lang/Object {
    I LESS = -1
    I EQUAL = 0
    I GREATER = 1
    compareTo (Ljava/lang/Object;)I
}
{% endcodeblock %}

# Loading a generated class
Loading a class which is generated on the fly is usually helpful when, say writing a dynamic proxy class generator. To do that, we need to define a class loader first:

{% codeblock lang:java %}
package asmexample;

public class MyClassLoader extends ClassLoader {
    public Class defineClass(String name, byte[] b) {
        return defineClass(name, b, 0, b.length);
    }
}
{% endcodeblock %}

This class loader supports loading class directly from steam:

{% codeblock lang:java %}

package asmexample;

public class LoadAClass {
    public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException {
        byte[] bytes = new ClassMaker().makeAnInterface();
        Class iComparable = new MyClassLoader().defineClass("asmexample.Comparable", bytes);
        System.out.println(iComparable.getCanonicalName() + "." + "LESS = " + iComparable.getField("LESS").getInt(null));
    }
}
{% endcodeblock %}

The result is as expected:

{% codeblock %}
asmexample.Comparable.LESS = -1
{% endcodeblock %}

