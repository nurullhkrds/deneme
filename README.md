	getScriptOfOkAction : function(){
			sb.fireEvent("setCallScriptParametersForOkAction", "btn_P265_Ok", null, function(){
				sb.retrieveCallScript("btn_P265_Ok", null, null, function(){
					sb.fireEvent("onOkActionClicked", "btn_P265_Ok", {}, null);
				}, this);
			});
		},
