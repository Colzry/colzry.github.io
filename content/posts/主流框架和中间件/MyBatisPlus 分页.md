---
title: "MyBatisPlus分页"
description: "MyBatisPlus分页"
keywords: "MyBatisPlus"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - 框架和中间件
tags:
  - MyBatisPlus
---

## 1. 添加分页插件
```java
@Configuration
public class MybatisPlusConfig {
    /**
     * 分页插件
     * @return
     */
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor());
        return interceptor;
    }
}

```
## 2. 使用
先定义PageParam
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class PageParam {

    private int page = 1;

    private int pageSize = 10;
}
```
### 2.1 使用mapper分页
```java
@Autowired
private DeptMapper deptMapper;

@Override
public Result getDeptList(PageParam pageParam) {
    Page<Dept> page = new Page<Dept>(pageParam.getPage(), pageParam.getPageSize());
    deptMapper.selectPage(page, null);
    return Result.success(page);
}
```

### 2.2 使用service分页
```java
@Autowired
private DeptServiceImpl deptService;

@PostMapping("list")
public Result deptPage(@RequestBody PageParam pageParam) {
    Page<Dept> page = new Page<>(pageParam.getPage(), pageParam.getPageSize());
    return Result.success(deptService.page(page));
}
```
### 2.3 自定义分页
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.colzry.oasystemserver.mapper.EmployeeMapper">
    <sql id="empVo">
        ${empTable}.id, emp_no, emp_name, emp_gender, emp_entry_time, dept_name
    </sql>

    <select id="queryBaseEmpPage" resultType="com.colzry.oasystemserver.vo.EmployeeVO">
        select
        <include refid="empVo">
            <property name="empTable" value="oa_employee"/>
        </include>
        from oa_employee
        left join oa_dept on oa_employee.emp_dept_id = oa_dept.id
    </select>
    <select id="queryBaseEmpPageByDept" resultType="com.colzry.oasystemserver.vo.EmployeeVO">
        select
        <include refid="empVo">
            <property name="empTable" value="oa_employee"/>
        </include>
        from oa_employee
        left join oa_dept on oa_employee.emp_dept_id = oa_dept.id
        where oa_dept.id = #{deptId}
    </select>
</mapper>
```
```java
@Mapper
@Repository
public interface EmployeeMapper extends BaseMapper<Employee> {
    IPage<EmployeeVO> queryBaseEmpPage(IPage<EmployeeVO> page);

    IPage<EmployeeVO> queryBaseEmpPageByDept(IPage<EmployeeVO> page,@Param("deptId") Long deptId);
}
```
```java
@Autowired
private EmployeeMapper employeeMapper;


@Override
public Result getBaseEmpList(PageParam pageParam) {
    Page<EmployeeVO> page = new Page<>(pageParam.getPage(), pageParam.getPageSize());
    IPage<EmployeeVO> empPage = employeeMapper.queryBaseEmpPage(page);
    return Result.success(empPage);
}

@Override
public Result getEmpListByDept(PageParam pageParam, Long deptId) {
    Page<EmployeeVO> page = new Page<>(pageParam.getPage(), pageParam.getPageSize());
    IPage<EmployeeVO> employeeVOPage = employeeMapper.queryBaseEmpPageByDept(page, deptId);
    return Result.success(employeeVOPage);
    }
```
