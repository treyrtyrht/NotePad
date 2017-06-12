# NotePadMay
initial
## 一.功能介绍：
### 1.时间戳</br>
### 2.便签搜索（根据标题模糊搜索）</br>
### 3.UI美化</br>
### 4.背景颜色更改(对话框弹出式)</br>
## 二.技术分析：
### 1.时间戳分析：</br>
因为数据库中已有时间字段，所以只需要格式化时间存入即可</br>
NoteEditor.updateNote()函数中修改</br>
        SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");//设置日期格式</br>
        values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, df.format(new Date()));
         ![image](http://a3.qpic.cn/psb?/V139AMkR4Vdea0/*GO9xy7hf*IdT4C0OOVpVktcwdq3pXIwiTlvzAR3qgI!/b/dG0BAAAAAAAA&bo=VwFvAlcBbwIDACU!&rf=viewer_4)
 ### 2.便签搜索
   private void addSearchView() 
   {
        //给listview添加头部(search)
        
        View v=View.inflate(this, R.layout.notelistheader,null);
        getListView().addHeaderView(v);
        //给搜索框添加搜索功能
        final EditText et_Search=(EditText)v.findViewById(R.id.et_search);
        et_Search.addTextChangedListener(new TextWatcherForSearch(){
            @Override
            public void onTextChanged(CharSequence charSequence, int i, int i1, int i2) {
                super.onTextChanged(charSequence, i, i1, i2);
                if (charSequence.length()!=0 && et_Search.getText().toString().length()!=0){
                    String str_Search = et_Search.getText().toString();
                    Cursor search_cursor = managedQuery(
                            getIntent().getData(),            // Use the default content URI for the provider.
                            PROJECTION,                       // Return the note ID and title for each note.
                            NotePad.Notes.COLUMN_NAME_TITLE+" like ?",     //模糊查询数据库
                            new String[]{"%"+str_Search+"%"}, //匹配字符串条件                            // No where clause, therefore no where column values.
                            NotePad.Notes.DEFAULT_SORT_ORDER  // Use the default sort order.
                    );
                    adapter.swapCursor(search_cursor);//用搜索结果的cursor刷新listview

                }else {
                    if (cursor!=null)//删除搜索框中的text后刷新listview
                    adapter.swapCursor(cursor);//刷新listview
                }
            }
        });
    }
     ![2](http://a3.qpic.cn/psb?/V139AMkR4Vdea0/C64DaQ8ukOZX3Zw5aqhoXbQbUqGqcqsiiJ0bJN2kU20!/b/dIIBAAAAAAAA&bo=RwFIAkcBSAIDACU!&rf=viewer_4)
 ### 3.改变背景颜色
    使用底部按钮弹出对话框
      private void setBackgroundTwo(){
        AlertDialog.Builder builder=new AlertDialog.Builder(NotesList.this);
        LayoutInflater inflater=getLayoutInflater();
        View view=inflater.inflate(R.layout.dialogcolor,null);
        LinearLayout linearLayout=(LinearLayout) view.findViewById(R.id.dialoglinear);
        final int[] colorArray={
                Color.parseColor("#707070"),//黑
                Color.parseColor("#fcf9a4"),//黄
                Color.parseColor("#abed65"),//绿
                Color.parseColor("#33a9e1"),//蓝
                Color.parseColor("#070707"),//黑
                Color.parseColor("#1cdaef"),//蓝绿
                Color.parseColor("#fa77ab"),//粉色
        };
        for (int i=0;i<7;i++){//动态创建imageview 用以显示不同颜色
            ImageView imageView=new ImageView(NotesList.this);
            imageView.setLayoutParams(new LinearLayout.LayoutParams(90,120));
            imageView.setBackgroundColor(colorArray[i]);
            final int finalI = i;
            imageView.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    NoteAttribute.snoteBackground=colorArray[finalI];
                    getListView().setBackgroundColor(NoteAttribute.snoteBackground);
                    getListView().setBackgroundColor(colorArray[finalI]);
                    appbackground
                            .edit()
                            .putInt(NoteAttribute.NOTEBACKGROUND,colorArray[finalI])
                            .apply();//使用轻量级存储sharepreference异步将颜色存储在本地
                }
            });
            linearLayout.addView(imageView);
        }
        builder.setView(view).create().show();

    }
     ![3](http://a1.qpic.cn/psb?/V139AMkR4Vdea0/bCiV3OChUGoQ0yepcK.SCWe9yUzqj6k*RgpmRPLXhrc!/b/dG4BAAAAAAAA&bo=RwFIAkcBSAIDACU!&rf=viewer_）
     4笔记导出  ![i](http://a3.qpic.cn/psb?/V139AMkR4Vdea0/z9WHLd79enCWqRVQUAlWyPWjBVObfiEzq.ylor.Ei.k!/b/dG0BAAAAAAAA&bo=QwFvAkMBbwIDACU!&rf=viewer_4)//设置文件路径
String filePath = Environment.getExternalStorageDirectory().getPath() + "/NotePad/ExportTxt/";
//保存文件
ToFile.writeTxtToFile(cursor.getString(colNoteIndex),filePath,cursor.getString(colTitleIndex));
通过writeTxtToFile可以预先创建文件夹并且创建文件时将笔记中的标题以及文本信息写入txt格式的文件当中。 注意：

<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
需要在AndroidManifest.xml添加上述代码，应用才具有文件写入权限。


