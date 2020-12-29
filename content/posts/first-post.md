---
title: "保密技术报告1 -- 多表替代加密"
subtitle: ""
date: 2020-12-10T00:28:09+08:00
lastmod: 2020-12-10T00:28:09+08:00
draft: false
author: ""
authorLink: ""
description: ""

tags: ["密码学"]
categories: ["课程报告"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: ""
featuredImagePreview: ""

toc:
  enable: true
math:
  enable: false
lightgallery: false
license: ""
---

<!--more-->

## **一、** **题目：多表替代加密算法的实现**  

<br/>

## **二、** **需要完成的任务：**  

<br/>

1. 了解多表替代加密的原理

2. 掌握多表替代加密的算法思想

3. 完成多表替代加密的实现

   <br/>

## **三、** **具体实现过程：**  

<br/>

### **基本知识点：**  

<br/>

1. 多表代替密码  

   <br/>

   多表代替密码是由多个简单的代替密码构成。例如，可能有5个被使用的不同的简单代替密码，单独的一个字符用来改变明文的每个字符的位置。其算法可简述为：设密钥为`k`,明文为`m`，加密为`c`，则有加密变换`ek(m)=c1c2…cn`，其中，`ci=mi+ki mod q`。本次作业采用了多表代替的经典密码--维吉尼亚密码作为研究对象。  

   <br/>

2. 维吉尼亚密码  

   <br/>

   维吉尼亚密码是使用一系列恺撒密码组成密码字母表的加密算法，属于多表密码的一种简单形式。在一个恺撒密码中，字母表中的每一字母都会作一定的偏移，例如偏移量为3时，A就转换为了D、B转换为了E……而维吉尼亚密码则是由一些偏移量不同的恺撒密码组成。  

   <br/>

   为了生成密码，需要使用表格法。这一表格（如下图所示）包括了26行字母表，每一行都由前一行向左偏移一位得到。具体使用哪一行字母表进行编译是基于密钥进行的，在过程中会不断地变换。  

   <br/>

<img src="https://i.loli.net/2020/12/29/mYRhW59uoVbwsdf.png#pic_center" alt="维吉尼亚密码表.png" align="center" style="zoom:200%;" />  

<br/>

### **算法流程**：  

<br/>

假设明文为：  

<br/>

`ATTACKATDAWN`  

<br/>

选择某一关键词并重复而得到密钥，如关键词为LEMON时，密钥为：  

<br/>

`LEMONLEMONLE`  

<br/>

对于明文的第一个字母A，对应密钥的第一个字母L，于是使用表格中L行字母表进行加密，得到密文第一个字母L。类似地，明文第二个字母为T，在表格中使用对应的E行进行加密，得到密文第二个字母X。以此类推，可以得到：  

<br/>

明文：`ATTACKATDAWN` 密钥：`LEMONLEMONLE` 密文：`LXFOPVEFRNHR`  

<br/>

解密的过程则与加密相反。例如：根据密钥第一个字母L所对应的L行字母表，发现密文第一个字母L位于A列，因而明文第一个字母为A。密钥第二个字母E对应E行字母表，而密文第二个字母X位于此行T列，因而明文第二个字母为T。以此类推便可得到明文。  

<br/>

用数字`0-25`代替字母`A-Z`，维吉尼亚密码的加密文法可以写成同余的形式（如下图）。

<br/>

![维吉尼亚算法描述.png](https://i.loli.net/2020/12/29/gRl67tbDyJcYMiV.png#pic_center)

  <br/>

### **具体实现代码 (Java)**  

<br/>

```java
package Homework_Vigenere;

public class Vigenere {

    /**大写字母表**/
    static String alphabet ="ABCDEFGHIJKLMNOPQRSTUVWXYZ";

    /**
     * 处理密钥
     * @param str 字符串
     * @param K 密钥
     * @return 与str长度相等的密钥字符串
     */
    public static String dealK(String str,String K){
        K=K.toUpperCase();// 将密钥转换成大写
        K=K.replaceAll("[^A-Z]", "");//去除所有非字母的字符
        StringBuilder sb=new StringBuilder(K);
        String key="";
        if(sb.length()!=str.length()){
            //如果密钥长度与str不同，则需要生成密钥字符串
            if(sb.length()<str.length()){
                //如果密钥长度比str短，则以不断重复密钥的方式生成密钥字符串
                while(sb.length()<str.length()){
                    sb.append(K);
                }
            }
            //此时，密钥字符串的长度大于或等于str长度
            //将密钥字符串截取为与str等长的字符串
            key=sb.substring(0, str.length());
        }
        return key;
    }

    /**
     * 根据vigenere密码算法对明文进行加密
     * @param P 明文
     * @param K 密钥
     * @return 密文
     */
    public static String encrypt(String P,String K){
        P=P.toUpperCase();// 将明文转换成大写
        P=P.replaceAll("[^A-Z]", "");//去除所有非字母的字符
        K=dealK(P,K);
        int len=K.length();
        StringBuilder sb=new StringBuilder();
        for(int i=0;i<len;i++){
            int row= alphabet.indexOf(K.charAt(i));//行号
            int col= alphabet.indexOf(P.charAt(i));//列号
            int index=(row+col)%26;
            sb.append(alphabet.charAt(index));
        }
        return sb.toString();
    }

    /**
     * 根据vigenere密码算法对密文进行解密
     * @param C 密文
     * @param K 密钥
     * @return 明文
     */
    public static String decrypt(String C,String K){
        C=C.toUpperCase();// 将密文转换成大写
        C=C.replaceAll("[^A-Z]", "");//去除所有非字母的字符
        K=dealK(C,K);
        int len=K.length();
        StringBuilder sb=new StringBuilder();
        for(int i=0;i<len;i++){
            int row= alphabet.indexOf(K.charAt(i));//行号
            int col= alphabet.indexOf(C.charAt(i));//列号
            int index;
            if(row>col){
                index=col+26-row;
            }else{
                index=col-row;
            }
            sb.append(alphabet.charAt(index));
        }
        return sb.toString();
    }

    public static void main(String args[]){
        String P="testvigenere";
        String K="encrypt";
        String C=encrypt(P,K);
        System.out.println("明文："+P);
        System.out.println("密钥："+K);
        System.out.println("密文："+C);
        System.out.println("解密后："+decrypt(C,K));
    }

}

```

  <br/>

### **实现结果：**  

<br/>

![测试结果.png](https://i.loli.net/2020/12/29/iYq8u1XNyJEwODt.png#pic_center)

 <br/>

## **四、** **心得体会：**  

<br/>

在这次任务查询资料的过程中，我了解了从恺撒密码到多表替代密码这一古典密码的发展史。重点研究了维吉尼亚密码的加解密方式，并顺带了解了一些关于暴力破解这种密码的知识。作为早期的加解密算法，维吉尼亚密码的使用方式简便，可靠程度较高，但仅支持对字母加密，总的来说，不失为一种经典的古典密码。