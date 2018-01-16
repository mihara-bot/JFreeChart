### time.jsp
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
    
    <title>My JSP 'time.jsp' starting page</title>
    
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
    	<img src="${graphURL }" boeder="1">
    	<br><br>
    	<a href="quyu.jsp">返回</a>
    </div>
  </body>
</html>

```
### time.java
```
package bean;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.jfree.chart.servlet.ServletUtilities;

public class _time extends HttpServlet {

	/**
	 * The doGet method of the servlet. <br>
	 *
	 * This method is called when a form has its tag value method equals to get.
	 * 
	 * @param request the request send by the client to the server
	 * @param response the response send by the server to the client
	 * @throws ServletException if an error occurred
	 * @throws IOException if an error occurred
	 */
	public void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {

		String fileName=ServletUtilities.saveChartAsJPEG(
				time.createChart(),600,300,request.getSession()
				);
	//	String graphURL=request.getContextPath()+"/DisplayChart?filename="+fileName;
	//	request.setAttribute("graphURL", graphURL);
	//	request.getRequestDispatcher("/time.jsp").forward(request, response);
		
		String graphURL=request.getContextPath()+"/servlet/DisplayChart?filename="+fileName;
		request.setAttribute("graphURL",graphURL);
		request.getRequestDispatcher("/time.jsp").forward(request,response);
	}

}

```
### time.java
```
package bean;

import java.awt.Color;
import java.awt.Font;
import java.text.DateFormat;
import java.text.DecimalFormat;
import java.text.SimpleDateFormat;

import org.hyperic.sigar.Mem;
import org.hyperic.sigar.Sigar;
import org.hyperic.sigar.SigarException;
import org.hyperic.sigar.Swap;
import org.jfree.chart.ChartFactory;
import org.jfree.chart.JFreeChart;
import org.jfree.chart.axis.DateAxis;
import org.jfree.chart.axis.DateTickUnit;
import org.jfree.chart.axis.DateTickUnitType;
import org.jfree.chart.axis.ValueAxis;
import org.jfree.chart.plot.XYPlot;
import org.jfree.data.time.Day;
import org.jfree.data.time.TimeSeries;
import org.jfree.data.time.TimeSeriesCollection;
import org.jfree.data.xy.XYDataset;

public class time {
	private static final Font plot_=new Font("宋体",Font.ITALIC,18);
	
	public static XYDataset createDataset(){
		TimeSeries time=new TimeSeries("Data");
		Day day=new Day(1,1,2008);
		double d=3000D;
		for(int i=0;i<365;i++){
			d=d+(Math.random()-0.5)*10;
			time.add(day,d);
			day=(Day)day.next();
		}
		TimeSeriesCollection timeSeries=new TimeSeriesCollection(time);
		return timeSeries;
	}
	public static JFreeChart createChart(){
		JFreeChart chart=ChartFactory.createTimeSeriesChart(
				"销量统计",
				"月份",
				"销量",
				createDataset(),
				true,
				true,
				false
				);
		chart.getTitle().setFont(new Font("隶书",Font.BOLD,26));
		chart.setBackgroundPaint(new Color(252,175,134));
		XYPlot plot=chart.getXYPlot();
		plot.setDomainGridlinesVisible(false);
		DateAxis dataAxis=(DateAxis)plot.getDomainAxis();
		dataAxis.setLabelFont(new Font("黑体",Font.ITALIC,18));
		dataAxis.setTickLabelFont(new Font("宋体",Font.PLAIN,12));
		dataAxis.setLowerMargin(0.0);
		ValueAxis value=plot.getRangeAxis();
		value.setLabelFont(plot_);
		DateFormat format=new SimpleDateFormat("MM月份");
		DateTickUnit du=new DateTickUnit(DateTickUnitType.DAY,29,format);
		dataAxis.setTickUnit(du);
		return chart;
		
	}
}

```
