---
layout: "page-fullwidth"
title: "Serialize JSON Data for Bootstrap-treeview"
subheadline: ASP.NET
teaser: "Dynamically populate bootstrap-treeview JSON data using ASP.NET JsonConvert."
breadcrumb: true
categories: 
  - webdev
tags: 
  - code snippet
  - "c#"
  - web application
  - asp.net
  - bootstrap
published: true
---


### Overview
Dynamically populate the [Bootstrap-treeview](https://github.com/jonmiles/bootstrap-treeview) data from a LINQ-to-SQL datacontext in an ASP.NET C# code behind. The function serialize db.text fields a LINQ query of two tables; one parent, the other child, ultimately merging the serialized a string into JSON parsable string. Function is then called by the front-end javascript to reconvert the serialized string using jQuery.ParseJson() method, becoming the data for the bootstrap treeview.

#### Bootstrap-treeview expected JSON format
{% highlight javascript %}
var tree = [
  {
    text: "Parent 1",
    nodes: [
      {
        text: "Child 1",
        nodes: [
          {
            text: "Grandchild 1"
          },
          {
            text: "Grandchild 2"
          }
        ]
      },
      {
        text: "Child 2"
      }
    ]
  },
  {
    text: "Parent 2"
  },
  {
    text: "Parent 3"
  },
  {
    text: "Parent 4"
  },
  {
    text: "Parent 5"
  }
];
{% endhighlight %}

#### Required Namespaces
{% highlight csharp %}
using System.Runtime.Serialization.Json;
using System.IO;
using System.Text;
using Newtonsoft.Json;
{% endhighlight %}

#### Object Class Instances
{% highlight csharp %}
public class ParentWithNoChild
{
    public string text { get; set; }
}

public class ParentWithChildren
{
    public string text { get; set; }
    public Child[] nodes { get; set; }
}

public class Child
{
    public string text { get; set; }
}
{% endhighlight %}

#### ExtensionMethods
Extension methods used to simplify formatting of final JSON data format of bootstrap-treeview
{% highlight csharp %}
public static class ExtensionMethods
{
    public static string GetTreeViewJsonFormat(this string str)
    {
        return '[' + str + ']';
    }
    public static string MergeJsonString(this string str, string strTwo)
    {
        if (String.IsNullOrEmpty(str))
            return strTwo;
        else
            return str + "," + strTwo;
    }
}
{% endhighlight %}

#### Code behind method
{% highlight csharp %}

public string GetJsonData()
{
    string serializeJsonData = string.Empty;  
    RAP_IRNSIdentityDataContext db = new RAP_IRNSIdentityDataContext();
    
    // Select all agencies (parent node)
    List<RapAgency> agencies = (from a in db.RapAgencies
                                select a).ToList();

    // Loop trough all agencies and serialize it as the parent                              
    int totalAgencies = agencies.Count();
    for (int i = 0; i < totalAgencies; i++)
    {
        // Select all subagencies (child nodes)
        List<RapSubAgency> subagencies = (from sa in db.RapSubAgencies
                                            where sa.RapAgencyId == agencies[i].Id
                                            select sa).ToList();
        // Parent node has no child, serialize as ParentWithNoChild object
        int totalSubAgencies = subagencies.Count();
        if (totalSubAgencies == 0)
        {
            ParentWithNoChild single = new ParentWithNoChild();
            single.text = agencies[i].Agency;
            serializeJsonData = serializeJsonData.MergeJsonString(JsonConvert.SerializeObject(single));
        }
        // Parent node has child(ren), serialize as ParentWithChildren object
        else if (totalSubAgencies >= 1)
        {
            ParentWithChildren parent = new ParentWithChildren();
            Child[] children = new Child[totalSubAgencies];

            parent.text = agencies[i].Agency;
            for (int j = 0; j < totalSubAgencies; j++)
            {
                //Response.Write("<br/></br> SubAgencies: " + subagencies[j].AgencyDept);
                if (!String.IsNullOrEmpty(subagencies[j].AgencyDept))
                {
                    Child child = new Child { text = subagencies[j].AgencyDept };
                    children[j] = child;
                }
            }
            parent.nodes = children;
            serializeJsonData = serializeJsonData.MergeJsonString(JsonConvert.SerializeObject(parent));
        }
    }
    return serializeJsonData.GetTreeViewJsonFormat();
}
{% endhighlight %}

This methods returns the serialized JSON data string.
{% highlight json %}
[{"text":"HISC - Hawaii Invasive Species Committee","nodes":[{"text":"Big Island Invasive Species Committee"},{"text":"Kauai Invasive Species Committee"},{"text":"Maui Invasive Species Committee"}]},{"text":"CTAHR - College of Tropical Agriculture and Human Resources"},{"text":"DAR - Division of Aquatic Resources"},{"text":"DLNR - Department of Land and Natural Resources"},{"text":"DOFAW - Division of Forestry and Wildlife"},{"text":"HDOA - Hawaii Department of Agriculture"},{"text":"HBIN - Hawaii Biodiversity Information Network","nodes":[{"text":"HBIN Web Application Developers"}]},{"text":"Maui Humane Society"},{"text":"National Park Service I&M"},{"text":"USGS - U.S. Geological Survey"}]   
{% endhighlight %}

#### Javascritp function call.
The Javascript function call which parses the serialized json string back to JSON format.
{% highlight javascript %}
var $tree = $('#treeview12').treeview({
                data: jQuery.parseJSON('<%=GetJsonData() %>')
            });
{% endhighlight %}

#### Current Issues
Having trouble passing selected tree nodes back to the code behind. 

---

###References
    1. https://github.com/jonmiles/bootstrap-treeview
