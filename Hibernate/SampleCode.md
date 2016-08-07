``` java
package mydb;

import java.util.List;
import java.util.Set;

import org.hibernate.*;

public class Hello {
	public void insert(Session session) {
		Customer c = new Customer();
		c.setUsername("aaa");
		c.setPassword("aaa");
		
		session.save(c);
		System.out.println("---Insert success.");
	}

	public void delete(Session session) {
		Customer c = (Customer)session.get(Customer.class, 4);
		if (c != null) {
			session.delete(c);
		}
		
		System.out.println("---Delete success.");
	}
	
	public void update(Session session) {
		Customer c = (Customer)session.get(Customer.class, 5);
		if (c != null) {
			c.setPassword("zzz");
		}
		
		System.out.println("---Update success.");
	}
	
	public void select(Session session) {
		Customer c = null;
		
		// get: 错误时返回null
		c = (Customer)session.get(Customer.class, 6);
		if (c != null) {
			System.out.println("User: " + c.getUsername() + " Password: " + c.getPassword());
		}
		
		// load: 错误时抛出异常
		try {
			c = (Customer)session.load(Customer.class, 7);
			System.out.println("User: " + c.getUsername() + " Password: " + c.getPassword());
		} catch(Exception e) {
			System.out.println(e);
		}
		
		System.out.println("---Select success.");
	}
	
	public void query(Session session) {
		String sql = "";
		Query query = null;
		
		// HQL		
		sql = "from Customer as c where c.username=:username";
		query = session.createQuery(sql);
		query.setString("username", "aaa");
		List<Customer> customerlist = query.list();
		for (Customer x : customerlist) {
			System.out.println(x.getId() + " " + x.getUsername());
		}		
		
		// select
		sql = "select username from Customer";
		query = session.createQuery(sql);
		List<String> stringlist = query.list();
		for (String x : stringlist) {
			System.out.println(x);
		}
		
		// select multi
		sql = "select id, username from Customer";
		query = session.createQuery(sql);
		List<Object[]> objList = query.list();
		for (Object[] x : objList) {
			System.out.println("user id: " + x[0] + " user name: " + x[1]);
		}
		
		// select + function
		sql = "select count(distinct username) from Customer";
		query = session.createQuery(sql);
		List<Long> countList = query.list();
		for (Long x : countList) {
			System.out.println("user distinct name count: " + x);
		}
		
		// 配置查询
		// 需要在src/mydb/Customer.hbm.xml中添加以下内容
		// <hibernate-mapping>
		// <class> ... </class>
		// <query name="myquery">
    	// <![CDATA[ from Customer ]]>
		// </query>
		// </hibernate-mapping>
		query = session.getNamedQuery("myquery");
		customerlist = query.list();
		for (Customer x : customerlist) {
			System.out.println(x.getId() + " " + x.getUsername());
		}
		
		// 导航查询
		Customer c = (Customer)session.get(Customer.class, 1);
		Set<CommodityOrder> orders = c.getCommodityOrders();
		for (CommodityOrder order : orders) {
			System.out.println(order.getOrderNumber());
		}
		
		System.out.println("---Query success.");
	}
	
	public static void main(String[] args) {
		Hello hello = new Hello();
		Session session = HibernateSessionFactory.getSession();
		Transaction ts = null;
		try {
			ts = session.beginTransaction();
			hello.insert(session);
			hello.delete(session);
			hello.update(session);
			hello.select(session);
			hello.query(session);
			ts.commit();
			
		}catch (Exception e) {
			ts.rollback();
			System.out.println(e);
		} finally {
			HibernateSessionFactory.closeSession();
		}
	}
}
```
