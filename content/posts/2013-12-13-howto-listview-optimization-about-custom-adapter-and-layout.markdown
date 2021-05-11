---
title: "How about listView optimization adapter and layout of custom"
date: 2013-12-13 23:08:46 +0800
categories:
- Android
---

Adapter 的作用就是 ListView 與資料之間的橋樑，當 ListView 中每一個 Item 顯示到螢幕上時，都會調用 Adapter 的 getView 方法返回一個View，因此當 ListView 在處理 Scroll 事件時，會反覆的調用 getView 方法去顯示頁面，使頁面不斷的重複建立相同的 View ，因此會產生記憶體不足而導致閃退或是滾動不夠順暢的狀況；由此可見我們應該要在調用 getView 方法時做些優化，以增進 ListView 的效能

ListView 優化的思路整理以下兩種：

1. View 的重復使用：  
View 每次創建是比較耗費時間的動作，因此對於 getView 方法傳入的 converView 應該充分利用 Null 的判斷
2. ViewHolder 的應用：  
View 在調用 findViewById 方法也是比較耗時的，因此需要考慮只調用一次，之後就調用 View.getTag 方法來取得 ViewHolder 物件

## BaseAdapter Example

繼承 BaseAdapter 之後需要重寫四個方法：getCount，getItem，getItemId 和 getView

1. BaseAdapter 一開始會先調用 getCount 來計算將會顯示的 Item 數，因此我們需先回傳該值
  <pre class="prettyprint java">
    @Override
    public int getCount() {
      // TODO Auto-generated method stub
      return rowItems.size();
    }
  </pre>

2. 為了反覆取用之前已建立過的 View，我們利用 ViewHolder 保存已建立的 View；這邊可以存取 Item 中所顯示的元件
  <pre class="prettyprint java">
    private class ViewHolder {
      ImageView imageView;
      TextView txtTitle;
      TextView txtDesc;
    }
  </pre>

3. 當 BaseAdapter 調用 getView 時，我們可以從 converView 中確認 View 是否存在，如果存在則從 ViewHolder 中取出，如果未存在則建立新的 View 並設定 tag 記錄已建立過；當指定好 Holder 後，便利用 Holder 去執行所需要的配置與相對應的事件了
  <pre class="prettyprint java">
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
      // TODO Auto-generated method stub
      ViewHolder holder = null;
      LayoutInflater mInflater =
        (LayoutInflater) context.getSystemService
        (Activity.LAYOUT_INFLATER_SERVICE);
      if(convertView == null){
        convertView = mInflater.inflate(R.layout.list_item, null);
        holder = new ViewHolder();
        holder.txtDesc = (TextView) convertView.findViewById(R.id.desc);
        holder.txtTitle = (TextView) convertView.findViewById(R.id.title);
        holder.imageView = (ImageView) convertView.findViewById(R.id.icon);
        convertView.setTag(holder);
      }else{
        holder = (ViewHolder) convertView.getTag();
      }
      RowItem rowItem = (RowItem) getItem(position);
      holder.txtDesc.setText(rowItem.getDesc());
      holder.txtTitle.setText(rowItem.getTitle());
      holder.imageView.setImageResource(rowItem.getImageId());
      return convertView;
    }
  </pre>

> BaseAdapter 完整範例的原始碼 [GitHub - NeoHsu/CustomListViewWithBaseAdapter](https://github.com/NeoHsu/CustomListViewWithBaseAdapter)

## CursorAdapter Example
<p></p>
使用 CursorAdapter 只需要重寫兩個方法：newView，bindView；CursorAdapter 和 BaseAdapter 有些許的不同，因為 BaseAdapter 主要是調用 getView 方法去建立與判斷 View 是否存在，而 CursorAdapter 有兩種方式： newView 和 bindView 可以被調用，newView 主要負責建立 View ， bindView 則是負責處理建立好的 View ，且由於 CursorAdapter 的資料是屬於動態綁定，因此對於建立好的 View 它並不會重新再建立一次，而是藉由 bindView 去對應資料與配置，與 BaseAdapter 運作方式有些許不同但實作的原理是相同的

<pre class="prettyprint java">
  class ContactAdapter extends CursorAdapter {
    private LayoutInflater mInflater;

    public ContactAdapter(Context context, Cursor c) {
      super(context, c);
      mInflater = LayoutInflater.from(context);
    }

    @Override
    public void bindView(View view, Context context, Cursor cursor) {
      ViewHolder holder = (ViewHolder)view.getTag();
      holder.name.setText(cursor.getString(holder.nameIndex));
      holder.number.setText(cursor.getString(holder.numberIndex));
    }

    @Override
    public View newView(Context context, Cursor cursor, ViewGroup parent) {
      View view = mInflater.inflate(R.layout.contactlist_row, parent, false);
      ViewHolder holder = new ViewHolder();
      holder.name = (TextView)view.findViewById(R.id.name);
      holder.number = (TextView)view.findViewById(R.id.number);
      holder.nameIndex = cursor.getColumnIndex("contact_name");
      holder.numberIndex = cursor.getColumnIndex("contact_number");
      view.setTag(holder);
      return view;
    }

    private static class ViewHolder {
      int nameIndex;
      int numberIndex;
      TextView name;
      TextView number;
    }
  }
</Pre>

## Summary
增加 ListView 效能的主要方式不外乎是減少 View 的建立與重複的使用已建立過的物件；由於 CursorAdapter 屬於動態資料綁定，因此當資料做修正後，並不需要去更新 Adapter 的對應；BaseAdapter 則需更新完資料後，調用 notifyDataSetChanged 方法才能更新 ListView
