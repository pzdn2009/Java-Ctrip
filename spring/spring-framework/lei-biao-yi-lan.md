# ONG 类表一览

## 0. 概述

此文的目的：

## 1. spring-beans

`org.springframework.beans.factory`

<table>
  <thead>
    <tr>
      <th style="text-align:left">Class</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>BeanFactory</code>
      </td>
      <td style="text-align:left">
        <ol>
          <li>IOC&#x5BB9;&#x5668;&#x7684;root&#x63A5;&#x53E3;&#xFF1B;</li>
          <li>&#x7BA1;&#x7406;&#x6240;&#x6709;&#x7684;Bean&#x5BF9;&#x8C61;&#xFF0C;&#x5305;&#x62EC;FactoryBean&#xFF1B;</li>
          <li>&#x63D0;&#x4F9B;&#x4E86;&#x67E5;&#x8BE2;<code>get&#x7CFB;&#x5217;</code>&#x3001;&#x5224;&#x65AD;&#x63A5;&#x53E3;<code>is&#x7CFB;&#x5217;</code>&#xFF1B;</li>
        </ol>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>HierarchicalBeanFactory</code>
      </td>
      <td style="text-align:left">
        <ol>
          <li>&#x5E26;&#x5C42;&#x6B21;&#x7ED3;&#x6784;&#x7684;Bean&#x5DE5;&#x5382;&#xFF0C;&#x662F;BeanFactory&#x7684;&#x6269;&#x5C55;&#x5B50;&#x63A5;&#x53E3;&#xFF1B;</li>
          <li>&#x4E3B;&#x8981;&#x8868;&#x73B0;&#x5728;<code>getParentBeanFactory</code>&#xFF1B;</li>
          <li><code>setParentBeanFactory</code>&#xFF0C;&#x5219;&#x5728;<code>ConfigurableBeanFactory</code>&#x63A5;&#x53E3;&#x4E2D;&#x63D0;&#x4F9B;&#xFF1B;</li>
        </ol>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>AutowireCapableBeanFactory</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>ListableBeanFactory</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">BeanDefinitionRegistry</td>
      <td style="text-align:left">
        <ol>
          <li>&#x5C01;&#x88C5;&#x4E86;&#x201C;&#x6CE8;&#x518C;&#x201D;&#x529F;&#x80FD;&#x7684;&#x552F;&#x4E00;&#x63A5;&#x53E3;&#xFF0C;&#x9488;&#x5BF9;Bean&#x6CE8;&#x518C;&#x800C;&#x8A00;&#xFF1B;</li>
          <li>&#x4E3B;&#x8981;&#x5C31;&#x662F;&#x653E;&#x7F6E;BeanDefinition&#xFF1B;&#x96C6;&#x5408;&#x64CD;&#x4F5C;&#xFF1B;</li>
          <li>&#x5728;<code>DefaultListableBeanFactory</code>&#x4E2D;&#x6709;&#x5B9E;&#x73B0;&#xFF0C;&#x6700;&#x7EC8;&#x4F1A;&#x653E;&#x5165;<code>beanDefinitionNames&#xFF0C;beanDefinitionMap</code>&#x4E2D;&#x5B58;&#x50A8;<code>(beanName, beanDefinition)</code>
          </li>
        </ol>
      </td>
    </tr>
  </tbody>
</table>

## 2. spring-core

| Class | Description |
| :--- | :--- |
| DefaultResourceLoader |  |
| ResourceLoader |  |
| Resource |  |

## 3. spring-context

<table>
  <thead>
    <tr>
      <th style="text-align:left">Class</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">AnnotationConfigApplicationContext</td>
      <td style="text-align:left">
        <ol>
          <li>&#x6807;&#x51C6;&#x7684;<code>ApplicationContext</code>&#xFF0C;&#x4E3B;&#x8981;&#x5904;&#x7406;&#x57FA;&#x4E8E;&#x6CE8;&#x89E3;&#x7684;Bean&#xFF1B;</li>
          <li><code>register&#xFF0C;scan&#xFF0C;registerBean</code>&#xFF1B;</li>
        </ol>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">AnnotationConfigRegistry</td>
      <td style="text-align:left">
        <ol>
          <li>&#x4E3A;&#x57FA;&#x4E8E;&#x6CE8;&#x89E3;&#x7684;ApplicationContext&#x5B9A;&#x4E49;&#x4E86;&#x516C;&#x5171;&#x7684;&#x63A5;&#x53E3;<code>register</code>&#xFF0C;<code>scan</code>&#xFF1B;</li>
          <li>&#x62BD;&#x8C61;&#x7684;&#x65B9;&#x9762;&#x4E3A;<code>class</code>&#x548C;<code>package</code>&#xFF1B;</li>
        </ol>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">GenericApplicationContext</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">AbstractApplicationContext</td>
      <td style="text-align:left">
        <ol>
          <li>&#x5E76;&#x6CA1;&#x6709;beanFactory&#x6570;&#x636E;&#x8F7D;&#x4F53;&#xFF1B;</li>
          <li>&#x901A;&#x8FC7;&#x5B50;&#x7C7B;&#x5B9E;&#x73B0;&#x62BD;&#x8C61;&#x65B9;&#x6CD5;<code>getBeanFactory()</code>&#x6765;&#x83B7;&#x53D6;&#xFF1B;</li>
        </ol>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">ConfigurableApplicationContext</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">ApplicationContext</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
    </tr>
  </tbody>
</table>



