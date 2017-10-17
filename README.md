###  _jdbc.jsp
```
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>
<%@ page import="org.jfree.chart.servlet.ServletUtilities,bean.jfree_jdbc" %>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <base href="<%=basePath%>">
    
    <title>My JSP '_jdbc.jsp' starting page</title>
    
	<meta http-equiv="pragma" content="no-cache">
	<meta http-equiv="cache-control" content="no-cache">
	<meta http-equiv="expires" content="0">    
	<meta http-equiv="keywords" content="keyword1,keyword2,keyword3">
	<meta http-equiv="description" content="This is my page">
	<!--
	<link rel="stylesheet" type="text/css" href="styles.css">
	-->

  </head>
  
  <body>
    <%
    	String fileName=ServletUtilities.saveChartAsJPEG(jfree_jdbc.createChart(),500,300,session);
    	String graphURL=request.getContextPath()+"/servlet/DisplayChart?filename="+fileName;
     %>
     <div align="center">
     	<img src="<%=graphURL%>" border="1">
     	<a href="quyu.jsp">返回</a>
     </div>
  </body>
</html>

```
### quyu.jsp
```
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <base href="<%=basePath%>">
    
    <title>My JSP 'quyu.jsp' starting page</title>
    
	<meta http-equiv="pragma" content="no-cache">
	<meta http-equiv="cache-control" content="no-cache">
	<meta http-equiv="expires" content="0">    
	<meta http-equiv="keywords" content="keyword1,keyword2,keyword3">
	<meta http-equiv="description" content="This is my page">
	<!--
	<link rel="stylesheet" type="text/css" href="styles.css">
	-->

  </head>
  
  <body>
    <div align="center">
    	<a href="servlet/_jdbc">jdbc大饼</a><br>
    	<a href="servlet/quyu">区域图</a><br>
    	<a href="servlet/_time">时序图</a><br>
    	<a href="servlet/_zhu">交换区</a><br>
    	<a href="servlet/_neicun">内存</a><br>
    	<a href="servlet/_cpu">cpu</a>
    </div>
  </body>
</html>

```
### jfree_jdbc.java
```
package bean;

import java.awt.Font;
import java.sql.SQLException;
import java.text.NumberFormat;

import org.jfree.chart.ChartFactory;
import org.jfree.chart.JFreeChart;
import org.jfree.chart.labels.StandardPieSectionLabelGenerator;
import org.jfree.chart.plot.PiePlot;
import org.jfree.data.general.PieDataset;
import org.jfree.data.jdbc.JDBCPieDataset;

public class jfree_jdbc {
	public static PieDataset initPieData() {  
        String driverName = "com.mysql.jdbc.Driver";   
        String url = "jdbc:mysql://localhost:3306/tb_shop";  
        String user = "root";      
        String password = "zhj1124.";   
        JDBCPieDataset dataset = null;  
        try {  
            
            dataset = new JDBCPieDataset(url,driverName, user, password);  
            String query = "select category,val from aaa";  
            dataset.executeQuery(query);  
         
            dataset.close();  
        } catch (Exception e) {  
            e.printStackTrace();  
        }   
        return dataset;  
    }  
    /**  
     * 创建饼形图实例  
     * @return JFreeChart对象  
     */ 
    public static JFreeChart createChart() {  
        JFreeChart chart = ChartFactory.createPieChart3D(  	//创建3D饼形图表  
                "暑假消费情况",  //图表标题 
                initPieData(),    //饼形图的数据集对象       
                true,     //是否显示图例               
                true,  //是否显示提示文本                 
                false);    
        
        chart.getTitle().setFont(new Font("隶书",Font.BOLD,25));  //设置标题字体         
        chart.getLegend().setItemFont(new Font("宋体",Font.BOLD,15));   //设置图例类别字体        
        PiePlot plot = (PiePlot) chart.getPlot();    //获取绘图区对象  
        plot.setForegroundAlpha(0.5f); //设置前景透明度   
        plot.setLabelFont(new Font("宋体",Font.PLAIN,12));     //设置分类标签的字体 
        plot.setCircular(true);  //设置饼形为正圆 
        //设置分类标签的格式  
        plot.setLabelGenerator(new StandardPieSectionLabelGenerator("{0}={2}",     //分别显示类别名称和百分比
                NumberFormat.getNumberInstance(),           //获取名称
                NumberFormat.getPercentInstance()));         //获取百分比
        return chart;  
    }  
} 
```
###  _jdbc.java
```
package bean;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.jfree.chart.servlet.ServletUtilities;

public class _jdbc extends HttpServlet {
	public void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		String fileName=ServletUtilities.saveChartAsJPEG(
				jfree_jdbc.createChart(),500,300,request.getSession()
				);
		String graphURL=request.getContextPath()+"/servlet/DisplayChart?filename="+fileName;
		request.setAttribute("graphURL",graphURL);
		request.getRequestDispatcher("/_jdbc.jsp").forward(request,response);
		
	}

}

```
### web.jsp
```
 <servlet>
		<servlet-name>DisplayChart</servlet-name>
		<servlet-class>org.jfree.chart.servlet.DisplayChart</servlet-class>
	</servlet>
	
	<servlet-mapping>
		<servlet-name>DisplayChart</servlet-name>
		<url-pattern>/servlet/DisplayChart</url-pattern>
	</servlet-mapping>
```
