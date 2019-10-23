驱动：Oracle.ManagedDataAccess.dll

优势：不使用客户端



```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using Oracle.ManagedDataAccess.Client;
using System.Data;
using System.Data.SqlClient;
using System.Configuration;

namespace Test.Ashx
{
    public class xx
    {
         private static string OraConnectionString = "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=xxxx)(PORT=1521))(CONNECT_DATA=(SERVICE_NAME=orcl)));Persist Security Info=True;User ID=xx;Password=xx;";
      //  private static string SqlConnectionString =   ConfigurationManager.ConnectionStrings["SQLConnectionString"].ConnectionString;

   

    /// <summary>
    /// 获取Sql Server连接字符串
    /// </summary>
        public static string SqlConnectionString
        {
            get
            {

                return ConfigurationManager.ConnectionStrings["ConnectionString"].ConnectionString;

            }
        }

    #region  基础操作

    /// <summary>
    /// Oracle中，执行查询语句，返回DataSet
    /// </summary>
    /// <param name="SQLString">查询语句</param>
    /// <returns>DataSet</returns>
    private static DataTable OraQuery(string SQLString)
    {
        using (OracleConnection connection = new OracleConnection(OraConnectionString))
        {
            DataSet ds = new DataSet();
            try
            {
                connection.Open();
                OracleDataAdapter command = new OracleDataAdapter(SQLString, connection);
                command.Fill(ds, "ds");
            }
            catch (Exception ex)
            {
                throw new Exception(ex.Message);
            }
            return ds.Tables[0];
        }
    }

    /// <summary>
    /// SQL Server中 执行SQL语句，返回影响的记录数
    /// </summary>
    /// <param name="SQLString">SQL语句</param>
    /// <returns>影响的记录数</returns>
    private static int SQLExecuteSql(string SQLString)
    {
        using (SqlConnection connection = new SqlConnection(SqlConnectionString))
        {
            using (SqlCommand cmd = new SqlCommand(SQLString, connection))
            {
                try
                {
                    connection.Open();
                    int rows = cmd.ExecuteNonQuery();
                    return rows;
                }
                catch (System.Data.SqlClient.SqlException e)
                {
                    connection.Close();
                    throw e;
                }
            }
        }
    }

    /// <summary>
    /// SQL Server 构建 SqlCommand 对象(用来返回一个结果集，而不是一个整数值)
    /// </summary>
    /// <param name="connection">数据库连接</param>
    /// <param name="storedProcName">存储过程名</param>
    /// <param name="parameters">存储过程参数</param>
    /// <returns>SqlCommand</returns>
    private static SqlCommand BuildQueryCommand(SqlConnection connection, string storedProcName, IDataParameter[] parameters)
    {
        SqlCommand command = new SqlCommand(storedProcName, connection);
        command.CommandType = CommandType.StoredProcedure;
        foreach (SqlParameter parameter in parameters)
        {
            if (parameter != null)
            {
                // 检查未分配值的输出参数,将其分配以DBNull.Value.
                if ((parameter.Direction == ParameterDirection.InputOutput || parameter.Direction == ParameterDirection.Input) &&
                    (parameter.Value == null))
                {
                    parameter.Value = DBNull.Value;
                }
                command.Parameters.Add(parameter);
            }
        }

        return command;
    }

    /// <summary>
    /// SQL Server 创建 SqlCommand 对象实例(用来返回一个整数值)	
    /// </summary>
    /// <param name="storedProcName">存储过程名</param>
    /// <param name="parameters">存储过程参数</param>
    /// <returns>SqlCommand 对象实例</returns>
    private static SqlCommand BuildIntCommand(SqlConnection connection, string storedProcName, IDataParameter[] parameters)
    {
        SqlCommand command = BuildQueryCommand(connection, storedProcName, parameters);
        command.Parameters.Add(new SqlParameter("ReturnValue",
            SqlDbType.Int, 4, ParameterDirection.ReturnValue,
            false, 0, 0, string.Empty, DataRowVersion.Default, null));
        return command;
    }

    /// <summary>
    /// SQL Server 执行存储过程，返回影响的行数		
    /// </summary>
    /// <param name="storedProcName">存储过程名</param>
    /// <param name="parameters">存储过程参数</param>
    /// <param name="rowsAffected">影响的行数</param>
    /// <returns></returns>
    private static int RunProcedure(string storedProcName, IDataParameter[] parameters)
    {
        using (SqlConnection connection = new SqlConnection(SqlConnectionString))
        {
            int result;
            connection.Open();
            SqlCommand command = BuildIntCommand(connection, storedProcName, parameters);
            //rowsAffected = command.ExecuteNonQuery();
            command.ExecuteNonQuery();
            result = (int)command.Parameters["ReturnValue"].Value;
            //Connection.Close();
            return result;
        }
    }

   

    //public string getConnOra()
    //{
    //    string str = "";
    //    try
    //    {
    //        using (OracleConnection conn = new OracleConnection(OraConnectionString))
    //        {
    //            conn.Open();
    //            String sql = "SELECT * FROM dual"; // 查询语句
    //            OracleDataAdapter adapter = new OracleDataAdapter(sql, conn);
    //            DataTable dt = new DataTable(); // 实例化数据表
    //            adapter.Fill(dt);
    //            foreach (DataRow dr in dt.Rows)
    //            {
                    
    //            }
    //            conn.Close(); // 关闭数据库连接
    //        }
    //    }
    //    catch (Exception ex)
    //    {
    //        str = "连接字符串解析为：" + OraConnectionString + ";出现错误，错误信息：" + ex.Message;
    //    }

    //    return str;
    //}

   

    }
}
```