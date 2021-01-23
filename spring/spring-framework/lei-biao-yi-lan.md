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
  </tbody>
</table>

## 2. spring-core



