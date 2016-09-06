
##mysql

``` java
import java.sql.*;

public class Hello {
    public static void main(String[] args) {
        try {
            Class.forName("com.mysql.jdbc.Driver");
            Connection cnn=DriverManager.getConnection("jdbc:mysql://localhost:3306/mydb","root","root");

            Statement st=cnn.createStatement();
            ResultSet rs=st.executeQuery("select * from aaa");
            while(rs.next()){
                int a=rs.getInt("a");
                int b=rs.getInt("b");
                System.out.println(a+" "+b);
            }
            rs.close();
            st.close();
            cnn.close();
        } catch (Exception ex) {
            System.out.println(ex);
        }
    }
}
```

---

``` java
import java.sql.*;

public class Hello {
    public static void main(String[] args) {
        try {
            Class.forName("com.mysql.jdbc.Driver");
            Connection cnn=DriverManager.getConnection("jdbc:mysql://localhost:3306/mydb","root","root");
            
            //可滚动但通常不受其他的更改影响、可更新结果集
            Statement st=cnn.createStatement(ResultSet.TYPE_SCROLL_INSENSITIVE,ResultSet.CONCUR_UPDATABLE);
            ResultSet rs=st.executeQuery("select a,b from aaa");
            rs.next();
            rs.updateInt("a", 10);
            rs.updateRow();
            
            rs.previous();
            while(rs.next()){            	
                int a=rs.getInt("a");
                if(rs.wasNull()) System.out.println("a:null");  //报告最后一个读取的列是否具有值 SQL NULL
                int b=rs.getInt("b");
                if(rs.wasNull()) System.out.println("b:null");
                System.out.println(a+" "+b);
//                System.out.print(a+" ");
//                if(rs.wasNull()) 
//                	System.out.println("null");
//                else
//                	System.out.println(b);
            }
            rs.close();
            st.close();
            cnn.close();
        } catch (Exception ex) {
            System.out.println(ex);
        }
    }
}

```

##带参的SQL语句

``` java
import java.sql.*;

public class Hello {
    public static void main(String[] args) {
        try {
            Class.forName("com.mysql.jdbc.Driver");
            Connection cnn=DriverManager.getConnection("jdbc:mysql://localhost:3306/mydb","root","root");

            PreparedStatement ps=cnn.prepareStatement("insert into aaa values(?,?)");
            ps.setInt(1,33);
            ps.setInt(2,33);
            ps.executeUpdate();
            
            Statement st=cnn.createStatement();
            ResultSet rs=st.executeQuery("select * from aaa");
            while(rs.next()){
                int a=rs.getInt("a");
                int b=rs.getInt("b");
                System.out.println(a+" "+b);
            }
            
            ps.close();
            st.close();
            cnn.close();
        } catch (Exception ex) {
            System.out.println("XXX");
        }
    }
}
```

##元数据

``` java
import java.sql.*;

public class Hello {
    public static void main(String[] args) {
        try {
            Class.forName("com.mysql.jdbc.Driver");
            Connection cnn=DriverManager.getConnection("jdbc:mysql://localhost:3306/mydb","root","root");
            
            String sql="select * from aaa" ;
            PreparedStatement ps = cnn.prepareStatement(sql);
            ResultSet rs = ps.executeQuery();

            ResultSetMetaData rsmd = rs.getMetaData();
            int count = rsmd.getColumnCount();
            for(int i=1; i<=count; i++){
            	System.out.println( rsmd.getColumnName(i) +
            		  "(" + rsmd.getColumnTypeName(i) + ")" );
            }

            rs.close();
            ps.close();
            cnn.close();
        } catch (Exception ex) {
            System.out.println(ex);
        }
    }
}
```

##事务

``` java
import java.sql.*;

public class Hello {
	public static void main(String[] args) {
		Connection cnn = null;

		try {
			Class.forName("com.mysql.jdbc.Driver");
			cnn = DriverManager.getConnection("jdbc:mysql://localhost:3306/mydb", "root", "root");
			
			cnn.setAutoCommit(false);
			PreparedStatement ps = cnn.prepareStatement("insert into aaa values(?,?)");
			ps.setInt(1, 6);
			ps.setInt(2, 6);
			ps.executeUpdate();
			//cnn.commit();
			ps.setInt(1, 1);
			ps.setInt(2, 1);
			ps.executeUpdate();
			cnn.commit();//如果不提交，虽然下面可以显示出来，但数据库中不会保存

			Statement st = cnn.createStatement();
			ResultSet rs = st.executeQuery("select * from aaa");
			while (rs.next()) {
				int a = rs.getInt("a");
				int b = rs.getInt("b");
				System.out.println(a + " " + b);
			}

			ps.close();
			st.close();
			cnn.close();
		} catch (Exception e) {
			System.out.println("1: "+e);
			try {
				cnn.rollback();
			} catch (SQLException e2) {
				System.out.println("2: "+e);
			}
		}
	}
}
```

##mysql存储过程

``` sql
delimiter //
create procedure myp(out i int,in j int)  
  begin 
    select a into i from aaa where b=j;
    select i;
  end
  //  
delimiter ;
```

``` java
import java.sql.*;

public class Hello {
    public static void main(String[] args) {
        try {
	    Class.forName("com.mysql.jdbc.Driver");
	    Connection cnn = DriverManager.getConnection("jdbc:mysql://localhost:3306/mydb", "root", "root");
            
            CallableStatement cs=cnn.prepareCall("{call myp(?,?)}");
            cs.registerOutParameter(1,java.sql.Types.INTEGER);
            cs.setInt(2,1);
            cs.execute();
            int i=cs.getInt(1);
            System.out.println(i);

            cnn.close();
        } catch (Exception ex) {
            System.out.println(ex);
        }
    }
}
```

##mysql中的时间类型

``` sql
DROP TABLE IF EXISTS `abc`;
CREATE TABLE `abc` (
  `id` int(11) NOT NULL,
  `a` date default NULL,
  `b` time default NULL,
  `c` datetime default NULL,
  PRIMARY KEY  (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=gbk;

INSERT INTO `abc` VALUES (1,'2011-3-01','06:08:01','2011-11-05 08:15:08');
```

---

``` java
import java.sql.*;
import java.text.SimpleDateFormat;

public class Hello {
	public static void main(String[] args) {
		try {
			Class.forName("com.mysql.jdbc.Driver");
			Connection cnn = DriverManager.getConnection(
					"jdbc:mysql://localhost:3306/mydb", "root", "root");

			Statement st = cnn.createStatement();
			ResultSet rs = st
					.executeQuery("select adddate(a,-3) a ,b,c from abc");
			while (rs.next()) {
//				String a = rs.getString("a");
//				String b = rs.getString("b");
//				String c = rs.getString("c");
//				System.out.println(a + " * " + b + " * " + c);

				java.sql.Date a = rs.getDate("a");
				Time b = rs.getTime("b");
				java.sql.Date c1 = rs.getDate("c");
				Time c2 = rs.getTime("c");
				System.out.println(a + " --- " + b + " --- " + c1 + " " + c2);

//				Calendar gc = Calendar.getInstance();
//				gc.setTime(c1);
//				System.out.println(gc.get(Calendar.YEAR));
//				gc.setTime(c2);
//				System.out.println(gc.get(Calendar.HOUR));
				
				Timestamp c=rs.getTimestamp("c");
				System.out.println(c);
				System.out.println(new SimpleDateFormat("yyyy").format(c));
				System.out.println(new SimpleDateFormat("HH").format(c));
			}
			rs.close();
			st.close();
			cnn.close();
		} catch (Exception ex) {
			System.out.println(ex);
		}
	}
}
```

##blob

``` sql
create table aaa(a int,b blob);
```

---

``` java
import java.sql.*;
import java.io.*;

public class Hello {
    public static void main(String[] args) {
        try {
            Class.forName("com.mysql.jdbc.Driver");
            Connection cnn=DriverManager.getConnection("jdbc:mysql://localhost:3306/mydb","root","root");
                        
            PreparedStatement ps=cnn.prepareStatement("insert into aaa values(?,?)");
            ps.setInt(1, 1);
            FileInputStream in=new FileInputStream("e:/temp/a.jpg"); //jpg
            byte[] jpg=new byte[in.available()];
            in.read(jpg);
            ps.setBytes(2, jpg);
            ps.executeUpdate();           
                       
            ResultSet rs=ps.executeQuery("select * from aaa where a=1");
            while(rs.next()){
                int a=rs.getInt("a");
                byte[] b=rs.getBytes("b");
                FileOutputStream out=new FileOutputStream("e:/temp/b.jpg");
                out.write(b);
                System.out.println(a);            
            }
            
            rs.close();
            ps.close();
            cnn.close();
        } catch (Exception ex) {
            System.out.println(ex);
        }
    }
}
```
