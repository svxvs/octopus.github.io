---
layout: post
title:  "PHP验证第二代身份证号"
date:   2016-11-7 19:37:03 +0800
categories: jekyll update
---

第二代身份证有着内在的因子算法，其依据于 中华人民共和国国家标准《公民身份号码》（GB11643-1999） ，我们可以根据该标准构建我们的身份证验证算法

```
/**:根据 中华人民共和国国家标准《公民身份号码》（GB11643-1999） 进行初步验证
 *!建议之后进一步连接至公安部身份证数据库进行验证
 *!该方法只支持 18 位身份证
 * @param $id_card 身份证号
 * @return array ['status'=>boolean,'msg'=>string] 数组：状态+提示
 */
function checkIdCard($id_card)
{
    $msg_false='身份证号错误！';
    $msg_warning='不支持15位身份证，请在派出所更换身份证。';
    $msg_true='身份证号正确！';
    //先判断长度
    $length=strlen($id_card);
    if( $length == 15
        || $length == 18
    );
    else
    {
        return array(
            'status'=>false,
            'msg'=>$msg_false
        );
    }
    //如果是15位，先判断是否符合15位正则
    if($length == 15)
    {
        $pattern_15="/^[1-9]\d{5}\d{2}((0[1-9])|1[012])(0[1-9]|[12][0-9]|3[01])\d{3}$/";
        if(preg_match($pattern_15,$id_card))
            return array(
                'status'=>false,
                'msg'=>$msg_warning
            );
        else
            return array(
                'status'=>false,
                'msg'=>$msg_false
            );
    }
    if($length == 18)
    {
        //先判断是否符合18位正则
        $pattern_18="/^[1-9]\d{5}[1-9]\d{3}((0[1-9])|1[012])(0[1-9]|[12][0-9]|3[01])\d{3}(\d|x|X)$/";
        if(!preg_match($pattern_18,$id_card))
            return array(
                'status'=>false,
                'msg'=>$msg_false
            );
    }
    //大区域编码验证
    $area_code_arr=array(11,12,13,14,15,21,22,23,31,32,33,34,35,36,37,41,42,43,44,45,46,50,51,52,53,54,61,62,63,64,65,71,81,82,91);
    $area_code=substr($id_card,0,2);
    if(!in_array($area_code,$area_code_arr))
        return array(
            'status'=>false,
            'msg'=>$msg_false
        );
    //验证日期是否存在，包括不能是当天往后，在闰年2月不能大于29日，非闰年2月不能大于28日
    //获取身份证号年月日期
    $the_date=substr($id_card,6,8);
    $year=substr($the_date,0,4);
    $month=ltrim(substr($the_date,4,2),'0');
    $day=ltrim(substr($the_date,6,2),'0');
    //检验
    if(!checkdate($month,$day,$year))
        return array(
            'status'=>false,
            'msg'=>$msg_false
        );
    //验证18位身份证：算出 尾数，进行验证。
    $fator_arr=array(7,9,10,5,8,4,2,1,6,3,7,9,10,5,8,4,2);
    $temp=array();
    for($j=0;$j<17;$j++)
    {
        $temp[]=$id_card[$j]*$fator_arr[$j];
    }
    $temp=array_sum($temp)%11;
    $verify_arr=array('1','0','X','9','8','7','6','5','4','3','2');
    if($verify_arr[$temp] == $id_card[17])
        return array(
            'status'=>true,
            'msg'=>$msg_true
        );
    else
        return array(
            'status'=>false,
            'msg'=>$msg_false
        );
}
```
