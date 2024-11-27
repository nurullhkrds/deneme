package com.ykb.hmn.migration.CorporateDefinition.controllers;

import java.io.Serializable;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import com.ykb.hmn.fw.core.adapter.contexts.ConversationContextManager;
import com.ykb.hmn.fw.core.adapter.contexts.DisplayContext;
import com.ykb.hmn.fw.core.adapter.contexts.EventData;
import com.ykb.hmn.fw.core.exceptions.FWWidgetException;
import com.ykb.hmn.fw.page.controller.PageController;
import com.ykb.hmn.fw.widget.table.ITable;
import com.ykb.hmn.migration.util.MessagesUtil;
import com.ykb.hmn.migration.util.StringUtils;


/**
 * @author U0T2556
 *
 * @param <T2>
 */
public abstract class AbstractListViewController<T2> implements Serializable {//T1 -> sayfanın classı, T2 kullanacağımız DTO'nun
	/**
	 * 
	 */
	private static final long serialVersionUID = 681089635560275654L;
	
	private ITable listView;
	private PageController currentPage;
	private Map<Integer,ListViewColumn> columns=new HashMap<Integer,ListViewColumn>();
	private String defaultCell="D";
	private String insertedCell="I";
	private String updatedCell="U";
	private boolean changeInvalidCellStyle=true;
	private String hiddenStatusColumn="col_hidden_list_controller_status^X0^L";
	private String selectionColumn="col_row_selection^K4^L^E;";
	private Integer statusColumnIndex=null;
	private boolean isDirtyCell=false;
	private int lastRowIndex;
	private int lastColumnIndex;
	private String listTempFormat="";
	private boolean approvementMode=false;
	///////////Mesajların değiştirebilinmesi için protected//////
	protected String EMPTY_CELL=" kolonu boş bırakılamaz.";
	protected String ONLY_NUMBER=" alanına sadece sayı girişi yapılabilir.";
	protected String ONLY_BIGDECIMAL=" alanına sadece ondalıklı sayı girilebilir.";
	protected String MAX_LENGTH=" alanına en fazla <maxLength> karakter girebilirsiniz.";
	//////////////////////////////////////////////////////////
	
	//////////Abstract methods/////////////
	/**
	 * ListView'da satırın dto'ya nasıl çevrileceğini belirleyen fonksiyondur.
	 * rowNum ile belirtilen satırdaki bilgi, listeden okunup DTO'ye maplenmelidir.
	 * 
	 * @param currentList
	 * @param rowNum
	 * @param rowStatus
	 * @return
	 */
	protected abstract T2 mapListViewRow2DTO(ITable currentList,int rowNum,String rowStatus);//Listview satırını DTO'ya nasıl mapleyeceğiz.
	
	/**
	 * DTO'nun nasıl listview row stringine çevrileceğini belirleyen fonksiyondur.
	 * return dto.getName+ITable.DELIMITER+dto.getSurName() gibi bir şeyler.
	 * 
	 * @param sourceDTO
	 * @return
	 */
	protected abstract String mapDto2ListViewRow(T2 sourceDTO);//DTO'u listview satırına nasıl dönüştüreceğiz
	
	
	/**
	 * ListView'a yeni bir kısmen boş satır eklemek istediğimizde, hangi değerlerin default geleceğini belirler.
	 * Tüm satır boş gelecekse return new XDTO() yapılabilir.
	 * @return
	 */
	protected abstract T2 getBlankRowDefaultValues();
	
	
	/**
	 * Kayıtların database'den getirilmesi bu method'da sağlanır.
	 * İlgili servis çağırılıp List<DTO> dönülmelidir.
	 * 
	 * @return
	 */
	protected abstract List<T2> listData() throws Exception;
	
	
	/**
	 * Parametredeki dto'nun database'den silinmesini sağlar.
	 * Listview'dan silmeye çalışmayın o otomatik yapılacaktır.
	 * 
	 * @param dto
	 * @throws Exception 
	 */
	protected abstract void deleteFromDatabase(T2 dto) throws Exception;
	//////////////////////////////
	
	////////Private methods/////////
	private void addRow2ListView(String val,T2 dto) throws FWWidgetException{
		listView.addRow(val);
		
		//Listeye satırı ekledikten sonra ComboBox kolonlar için
		//setCellValue diyerek değerini tekrar vermek gerekiyor
		//bu işlem yapılmazsa combo değiştirildiğinde ekran hara veriyor
		/*if(listView.getFormat().contains("^M")){ // eğer listede combo column varsa
			int lastRow=listView.getNumRow()-1;// son satırı düzeltmek gerekli
			String cols[]=listView.getFormat().split(";");
			String vals[]=val.split(ITable.DELIMITER);
			
			for(Integer i=0;i<cols.length;i++){
				if(cols[i].contains("^M")){//combocolumn
					int index=vals[i].indexOf("|");
					String value="0";
					
					if(index!=i && isNumeric(vals[i].substring(0,index)));
						value=vals[i].substring(0,index);
					
					listView.setCell(lastRow, i,value );
				}
			}
		}*/
		
	}
	
	protected boolean isBigDecimal(String decimal,Integer scale,Integer precision, boolean isNullable){
		if (StringUtils.hasNotText(decimal)) {
            return isNullable;
        }
		
		if(precision==null) precision=2;
		if(scale==null) scale=18;
		
        boolean isValid = false;
        
        decimal=decimal.replaceAll("\\.", "");
        String expression = "\\d{1,"+scale.toString()+"}+(\\,\\d{1,"+precision.toString()+"})?";
        CharSequence inputStr = decimal;
        Pattern pattern = Pattern.compile(expression);
        Matcher matcher = pattern.matcher(inputStr);
        if (matcher.matches()) {
            isValid = true;
        }
        return isValid;
	}
	
	private  String dto2Row(String status,T2 sourceDTO){
		return "0"+ITable.DELIMITER+mapDto2ListViewRow(sourceDTO)+ITable.DELIMITER+status;//son kolon status kolondur
	}
	
	private  List<T2> listView2DTOByStatu(String status){
		List<T2> list=new ArrayList<T2>();
		
		for (int i = 0; i < listView.getNumRow(); i++) {
			T2 currentRow=mapListViewRow2DTO(listView,i,getRowStatus(i));
			
			if(status.equals(getRowStatus(i)))
				list.add(currentRow);
		}
		
		return list;
	}
	
	private void refreshStatusIndex(){
		if(statusColumnIndex!=null && "DUI".contains(listView.getCell(0, statusColumnIndex)) ) return;
		
		String[] columns=listView.getFormat().split(";");
		for (int i = 0; i < columns.length; i++) {
			if(columns[i].equals(hiddenStatusColumn))
			{
				statusColumnIndex=i;
			}
		}
	}
	
	private boolean isNumeric(String value){
		try{
			if(value==null || StringUtils.hasNotText(value)) return true;
			
			Long.parseLong(value);
			return true;
		}catch(Exception e){
			return false;
		}
	}
	//////////////////////////////////
	
	////////Protected methods////////
	protected void invalidateAndClearCell(String msg,Integer rowIndex,Integer cellIndex,EventData events, DisplayContext dc, ConversationContextManager cc){
		invalidateCell(msg,rowIndex,cellIndex,events, dc, cc);
		clearCell(rowIndex,cellIndex,events, dc, cc);
	}
	
	protected void clearCell(Integer rowIndex,Integer cellIndex,EventData events, DisplayContext dc, ConversationContextManager cc){
		listView.setCell(rowIndex, cellIndex, "");
	}
	
	protected void clearCellStyle(Integer rowIndex,Integer cellIndex){
		listView.setCellStyle(rowIndex, cellIndex, "");
	}
	
	protected void invalidateCell(String msg,Integer rowIndex,Integer cellIndex,EventData events, DisplayContext dc, ConversationContextManager cc){
		MessagesUtil.addError(msg, events);
		
		if(isChangeInvalidCellStyle()==true){
			ListViewColumn column= columns.get(cellIndex);
			
			if(column!=null && column.isNullable()==false)
				listView.setCellStyle(rowIndex, cellIndex, "cell-warning");
			else{
				isDirtyCell=true; 
				lastRowIndex=rowIndex;
				lastColumnIndex=cellIndex;
				listView.setCellStyle(rowIndex, cellIndex, "cell-info");
			}
				

			System.out.println(listView.getCellStyle(rowIndex, cellIndex));
		}
	}
	
	protected PageController getPage(){
		return currentPage;
	}
	
	protected void addColumn(ListViewColumn column){
		columns.put(column.getColumnIndex(), column);
	}
	
	protected ListViewColumn getColumn(Integer columnIndex){
		return columns.get(columnIndex);
	}
	////////////////////////////////
	
	
	
	////////Public methods////////////
	public boolean isChangeInvalidCellStyle() {
		return changeInvalidCellStyle;
	}
	
	public  void setChangeInvalidCellStyle(boolean changeInvalidCellStyle) {
		this.changeInvalidCellStyle = changeInvalidCellStyle;
	}
	
	public AbstractListViewController(PageController page,ITable listView) throws Exception {
		if(listView==null){
			throw new Exception("ListView null olamaz!");
		}
		
		currentPage=page;
		this.listView=listView;
		
		//Otomatik gizli bir status kolonu ekler
		String listFormat=listView.getFormat();
		if(listFormat!=null && listFormat.contains(hiddenStatusColumn)==false){
			
			if(listFormat.endsWith(";")==false)
				listFormat=listFormat+";";
			
			listFormat=listFormat+hiddenStatusColumn;
			
			
			listView.setFormat(listFormat);

		}
		
		//en başa "Seç" kolonu eklenecek
		if(listFormat!=null && listFormat.contains(selectionColumn)==false){
			listFormat=selectionColumn+listFormat;
			listView.setFormat(listFormat);
		}
		
	}
	
	public boolean cellChanged(Integer rowIndex,Integer cellIndex,EventData events, DisplayContext dc, ConversationContextManager cc){
		String status=getRowStatus(rowIndex);
		if(isDirtyCell==true){
			listView.setCellStyle(lastRowIndex,lastColumnIndex , "");
			isDirtyCell=false;
		}
		
		//Databaseden gelen bir cell ise statusu updated olması gerekli
		if("D".equals(status)){
			listView.setCell(rowIndex,statusColumnIndex , "U");
		}

		return validateCell(rowIndex,cellIndex,false,events, dc, cc);
	}

	public boolean validateCell(Integer rowIndex,Integer cellIndex,boolean isGeneralControl,EventData events, DisplayContext dc, ConversationContextManager cc){
		System.out.println(rowIndex.toString()+" : "+cellIndex.toString());
		String val=StringUtils.suppressNull(listView.getCell(rowIndex, cellIndex));
		String columnName=listView.getLocalizedTitle(cellIndex, cc);
		ListViewColumn column=columns.get(cellIndex);
		
		
		String status=getRowStatus(rowIndex);
		if(isChangeInvalidCellStyle()==true)
		{
			listView.setCellStyle(rowIndex, cellIndex, "");
			
			
			/* cellStyle'ı ayarladıktan sonra listview'daki o satırda bir şey değişmezse ayarladığımız cellStyle görünmüyor,
			 * Bunu önlemek adına, yani satırda bir şeyin değişmiş olması için statü kolonunun sonuna bir boşluk ekliyorum eklenmişse siliyorum
			 * Böylece satır güncellenmiş ve, style değişikliği yansımış oluyor
			 */
			listView.setCell(rowIndex, statusColumnIndex,status+(isStatuContainsSpace(rowIndex)==false?" ":""));
			System.out.println(listView.getCellStyle(rowIndex, cellIndex));
		}
		
		if(column.getColumnType()==ListViewColumnType.Combo) return true;
		
		if(column.isNullable()==false && StringUtils.hasNotText(val)){
			invalidateAndClearCell(columnName+EMPTY_CELL,rowIndex,cellIndex,events,dc,cc);
			return false;
		}else if(column.getColumnType()!=ListViewColumnType.BigDecimal && column.getScale()!=null && column.getScale()<val.length()){
			invalidateAndClearCell(columnName+MAX_LENGTH.replace("<maxLength>",column.getScale().toString()),rowIndex,cellIndex,events,dc,cc);
			return false;
        } else if (column.getColumnType() == ListViewColumnType.BigDecimal && !isBigDecimal(val, column.getScale(), column.getPrecision(), column.isNullable())) {
			invalidateAndClearCell(columnName+ONLY_BIGDECIMAL +" (Format: "+StringUtils.rpad("", column.getScale(), '#')+","+StringUtils.rpad("", column.getPrecision(), '#')+")",rowIndex,cellIndex,events,dc,cc);
			return false;
		}else if(column.getColumnType()==ListViewColumnType.Number && isNumeric(val)==false){
			invalidateAndClearCell(columnName+ONLY_NUMBER,rowIndex,cellIndex,events,dc,cc);
			return false;
		}
		
		return true;
	}
	
	public boolean validateRow(Integer rowid,EventData events, DisplayContext dc, ConversationContextManager cc){
		boolean isValidated=true;
		
		for(Integer key:columns.keySet()){
			isValidated= validateCell(rowid,key,true,events,dc,cc) && isValidated;
		}

		return isValidated;
	}
	
	public boolean validate(EventData events, DisplayContext dc, ConversationContextManager cc){
		boolean isValidated=true;
		for (int i = 0; i < listView.getNumRow(); i++) {
			isValidated=validateRow(i,events,dc,cc);
			if(!isValidated) break;
		}

		return isValidated;
	}
	
	private boolean isStatuContainsSpace(int rowIndex){
		refreshStatusIndex();
		String rowStatus=listView.getCell(rowIndex, statusColumnIndex);//ilk kolon statu kolonudur.	
		return rowStatus.contains(" ");
	}
	
	public String getRowStatus(int rowIndex){
		refreshStatusIndex();
		String rowStatus=listView.getCell(rowIndex, statusColumnIndex);//ilk kolon statu kolonudur.	
		return rowStatus.trim();
	}
	
	public  List<T2> listView2DTO(){
		List<T2> list=new ArrayList<T2>();
		
		for (int i = 0; i < listView.getNumRow(); i++) {
			T2 currentRow=mapListViewRow2DTO(listView,i,getRowStatus(i));
			list.add(currentRow);
		}
		
		return list;
	}
	
	public void fillFromDataSource(EventData events, DisplayContext dc,	ConversationContextManager cc) throws Exception{
		//Eğer onay için ListView non-editable moda alındıysa tekrar editable mod'a alınmalıdır.
		listView.removeAll();
		
		//List data user tarafından yazılan abstract method, kayıtları database'den ya da farklı bi yerden getiriyor
		List<T2> records=listData();
		
		if(records!=null){
			addList(records);
		}
	}
	
	public void addEmptyRow(EventData events, DisplayContext dc, ConversationContextManager cc) throws FWWidgetException{
		if(validate(events, dc, cc)){
			T2 dto=getBlankRowDefaultValues();
			addRow2ListView(dto2Row(insertedCell, dto),dto);
		}
		
	}
	
	public void addRow(T2 dto) throws FWWidgetException{
		addRow2ListView(dto2Row(defaultCell, dto),dto);
	}
	
	public void addList(List<T2> list) throws FWWidgetException{
		if(list==null) return;
		
		for (int i = 0; i < list.size(); i++) {
			T2 dto=list.get(i);
			addRow(dto);
		}
		
	}
	
	public void deleteRow(int rowIndex) throws Exception{
		if(insertedCell.equals(getRowStatus(rowIndex))==false){
			T2 dto=mapListViewRow2DTO(listView, rowIndex,getRowStatus(rowIndex));
			deleteFromDatabase(dto);
		}
		
		//silmeden önce kolonlardaki stilleri temizle
		//temizlenmezse aynı index'e yeni satır gelirse stiller kalıyor.
		for (Integer key: columns.keySet()) {
			clearCellStyle( rowIndex, columns.get(key).getColumnIndex());
		}
		listView.removeRow(rowIndex);
	}
	
	public void removeAll(){
		listView.removeAll();
	}
	
	public int getRowCount(){
		return listView.getNumRow();
	}
	
	public List<T2> getUpdatedRows(EventData events, DisplayContext dc,	ConversationContextManager cc){
		return listView2DTOByStatu(updatedCell);
	}
	
	public List<T2> getInsertedRows(EventData events, DisplayContext dc,	ConversationContextManager cc){
		return listView2DTOByStatu(insertedCell);
	}
	
	public List<T2> getDeletedRows(EventData events, DisplayContext dc,	ConversationContextManager cc) {
		
		return null;
	}
	
	public ITable getListView() {
		return listView;
	}	
	
	public boolean isChanged(EventData events, DisplayContext dc,	ConversationContextManager cc){
		String status=defaultCell;
		
		for (int i = 0; i < listView.getNumRow(); i++) {			
			if(status.equals(getRowStatus(i))==false)
				return true;
		}
		
		return false;
	}
	
	public void toNormalModeWithExistingData() throws Exception{
		List<T2> tableDatas=listView2DTO();
		if(approvementMode){
			listView.setFormat(listTempFormat);
			approvementMode=false;
		}
		removeAll();
		addList(tableDatas);
	}
	
	public void toNormalMode(EventData events, DisplayContext dc, ConversationContextManager cc) throws Exception{
		if(approvementMode){
			listView.setFormat(listTempFormat);
			approvementMode=false;
		}
	}
	
	public void toApprovementMode(List<T2> addedList) throws Exception{
		removeAll();

		if(approvementMode==false){//daha önce alanlar zaten non editable yapılmadıysa
			String listFormat=getListView().getFormat();
			//formatı yedekleyelim tekrar eski haline dönmek istersek
			this.listTempFormat=listFormat;
			
			//Listviewdaki tüm kolonları non editable yap.
			listFormat=listFormat.replaceAll("\\^E", "");
			listView.setFormat(listFormat);
			
			approvementMode=true;
		}
		
		addList(addedList);			
	}
	
	/////////////////////////////////////////
}
