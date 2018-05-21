---
title: hi kangkang
typora-root-url: ..
typora-copy-images-to: ../uploads/images
date: 2018-05-21 13:27:53
categories: 类目1
tags:
---

#### 图片

![屏幕快照 2018-05-18 下午6.25.03-6880508](/uploads/images/屏幕快照 2018-05-18 下午6.25.03-6880508.png)





#### code 

```java
package com.helijia.catcher.web.controller;

import com.helijia.catcher.web.model.SyncFileContext;
import com.helijia.catcher.web.service.SyncService;
import com.helijia.catcher.web.util.CommonUtil;
import com.helijia.catcher.web.vo.FileInfoVo;
import com.helijia.common.api.model.ApiException;
import com.helijia.common.api.util.Extras;
import com.helijia.common.api.util.Result;
import org.apache.commons.lang3.StringUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.*;

import java.text.ParseException;
import java.util.Calendar;
import java.util.List;
import java.util.Objects;

/**
 * <p>ClassName     InterActiveController
 * <p>Description
 * <p>Author        chonglou
 * <p>Version
 * <p>Date          2018/03/29 10:51
 */
@RestController
@RequestMapping("/sync")
public class SyncController {

    public static final Logger LOGGER = LoggerFactory.getLogger(SyncController.class);

    @Value("${catcher.helijia.appid}")        // 河狸家appid
    private String appId;

    @Value("${catcher.helijia.app.secret}")
    private String appSecret;              // 河狸家appSecrete


    @Autowired
    private SyncService syncService;


    /**
     * 提供斑马获取文件信息数据
     *
     * @param appId
     * @param date
     * @param type
     * @param sign
     * @return
     */
    @RequestMapping(value = "getList", method = {RequestMethod.POST, RequestMethod.GET})
    @ResponseBody
    public Result<List<FileInfoVo>> getListToBanma(@RequestParam(value = "appId") String appId,
                                                   @RequestParam(value = "date") String date,
                                                   @RequestParam(value = "type") String type,
                                                   @RequestParam(value = "sign") String sign) {

        Result<List<FileInfoVo>> result = new Result<>();

        // date 格式校验
//        checkDateFormat(date, "yyyyMMdd");
        String tmpDate = date;
        try {
            tmpDate = getLastDate(date, -1);
        } catch (ParseException e) {
            throw new ApiException(String.format(" the request param date=[%s] is error ", date)).build("00002", "date format error");
        }

        // 鉴权
        if (!checkAuth(sign, date, type)) {
            throw new ApiException(String.format("illegal request,appId=[%s],date=[%s],sign=[%s]", appId, date, sign)).build("00002", "illegal request");
        }

        // types 不能为空
        if (StringUtils.isBlank(type)) {
            return result.setSuccess(false).setApiMessage("type can not be null");
        }


        List<FileInfoVo> fileInfoVos = syncService.getFileList(new String[]{type}, tmpDate);
        result.setData(fileInfoVos);
        result.setSuccess(true);

        return result;
    }

    private String getLastDate(String dateStr, Integer beforeDay) throws ParseException {
        Calendar calendar = Calendar.getInstance();
        calendar.setTime(CommonUtil.parseDate(dateStr, "yyyyMMdd"));
        calendar.add(Calendar.DAY_OF_MONTH, beforeDay);  //设置时间
        return CommonUtil.formatDate(calendar.getTime(), "yyyyMMdd");
    }


    /**
     * 从斑马获取文件
     * 并进行数据分析（可选，依赖spi，默认不进行数据分析）
     *
     * @param date
     * @param type
     * @return
     */
    @RequestMapping("getFile")
    @ResponseBody
    public Result<Void> getFileFromBanma(
            @RequestParam(value = "type") String type,
            @RequestParam(value = "date", required = false) String date,
            @RequestParam(value = "extras", required = false) String extras            // 格式key:value;key:value
    ) {

        if (StringUtils.isBlank(date)) {
            // 同步文件日期为空,则默认日期为前一天
            date = CommonUtil.formatDate(CommonUtil.getBeforeDay(-1).getTime(), "yyyyMMdd");
        } else {
            // date不为空， 格式校验
            checkDateFormat(date, "yyyyMMdd");
        }

        // 同步文件
        SyncFileContext context = new SyncFileContext();
        context.setType(type);
        context.setDate(date);
        context.setExtras(buildExtras(extras));
        syncService.syncFiles(context);
        return new Result<Void>();
    }


    /**
     * 构建extras
     *
     * @param extrasStr
     * @return
     */
    private Extras buildExtras(String extrasStr) {
        Extras extras = new Extras();
        if (!StringUtils.isBlank(extrasStr)) {
            String[] entityStrArrray = extrasStr.split(";");
            for (String enntityStr : entityStrArrray) {
                String[] kv = enntityStr.split(":");
                if (kv.length == 2) {
                    extras.put(kv[0], kv[1]);
                }
            }
        }
        return extras;

    }

    /**
     * 鉴权
     *
     * @param
     * @param date
     * @param checkSign
     * @return
     */
    private boolean checkAuth(String checkSign, String date, String type) {
        String paras = "appId=" + this.appId + "&date=" + date + "&type=" + type;
        try {
            String sign = CommonUtil.buildSign(appSecret, "appId=" + this.appId, "&date=" + date, "&type=" + type);
            return Objects.equals(checkSign, sign);
        } catch (Exception e) {
            LOGGER.error(String.format("fail to checkAuth,appId=[%s],date=[%s],checkSign=[%s].error info=[%s]", appId, date, checkSign, e.getMessage()));
        }
        return false;
    }


    private void checkDateFormat(String date, String expireDateFormat) {
        // date 格式校验
        try {
            CommonUtil.parseDate(date, expireDateFormat);
        } catch (ParseException e) {
            throw new ApiException(String.format(" the request param date=[%s] is error ", date)).build("00002", "date format error");
        }
    }

    public void setAppSecret(String appSecret) {
        this.appSecret = appSecret;
    }

    public void setAppId(String appId) {
        this.appId = appId;
    }
}

```

