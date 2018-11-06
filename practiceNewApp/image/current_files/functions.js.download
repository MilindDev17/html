// make it a global variable so other scripts can access it
var booked_load_calendar_date_booking_options;

;(function($, window, document, undefined) {

	$('.booked_calendar_chooser').change(function(e){

		e.preventDefault();

		var $selector 			= $(this),
			booked_ajaxURL		= $('#data-ajax-url').html(),
			thisCalendarWrap 	= $selector.parents('.booked-calendar-shortcode-wrap').find('.booked-calendar-wrap'),
			args				= {'load':'calendar_month',gotoMonth:false, calendar_id:$selector.val() };

		savingState(true,thisCalendarWrap);
		thisCalendarWrap.load(booked_ajaxURL, args, function(){
			adjust_calendar_boxes();
		 	$(window).trigger('booked-load-calendar', args, $selector );
		 });

		return false;

	});

	var $win = $(window);

	$.fn.spin.presets.booked = {
	 	lines: 10, // The number of lines to draw
		length: 7, // The length of each line
		width: 5, // The line thickness
		radius: 11, // The radius of the inner circle
		corners: 1, // Corner roundness (0..1)
		rotate: 0, // The rotation offset
		direction: 1, // 1: clockwise, -1: counterclockwise
		color: '#555', // #rgb or #rrggbb or array of colors
		speed: 1, // Rounds per second
		trail: 60, // Afterglow percentage
		shadow: false, // Whether to render a shadow
		hwaccel: false, // Whether to use hardware acceleration
		className: 'booked-spinner', // The CSS class to assign to the spinner
		zIndex: 2e9, // The z-index (defaults to 2000000000)
		top: '50%', // Top position relative to parent
		left: '50%' // Left position relative to parent
	}

	$win.on('load', function() {

		var ajaxRequests = [];

		// Fixes the URL with some themes wrapping the URL in <strong> tags, etc.
		setTimeout(function(){
			var realURL = $('#data-ajax-url *').contents().unwrap();
		},1);
		// END URL FIX

		// Calendar Next/Prev Click
		$('.booked-calendar-wrap').on('click', '.page-right, .page-left, .monthName a', function(e) {

			e.preventDefault();

			var $button 			= $(this),
				gotoMonth			= $button.attr('data-goto'),
				booked_ajaxURL		= $('#data-ajax-url').html(),
				thisCalendarWrap 	= $button.parents('.booked-calendar-wrap')
				calendar_id			= $button.parents('table.booked-calendar').attr('data-calendar-id'),
				args				= {'load':'calendar_month','gotoMonth':gotoMonth,'calendar_id':calendar_id};

			savingState(true,thisCalendarWrap);
			thisCalendarWrap.load(booked_ajaxURL, args, function(){
				adjust_calendar_boxes();
			 	$(window).trigger('booked-load-calendar', args, $button );
			});

			return false;

		});

		// Calendar Date Click
		$('.booked-calendar-wrap').on('click', 'tr.week td', function(e) {

			e.preventDefault();

			var $thisDate 			= $(this),
				$thisRow			= $thisDate.parent(),
				date				= $thisDate.attr('data-date'),
				booked_ajaxURL		= $('#data-ajax-url').html(),
				calendar_id			= $thisDate.parents('table.booked-calendar').attr('data-calendar-id'),
				colspanSetting		= $thisRow.find('td').length;

			if ($thisDate.hasClass('blur') || $thisDate.hasClass('booked') || $thisDate.hasClass('prev-date')){

				// Do nothing.

			} else if ($thisDate.hasClass('active')){

				$thisDate.removeClass('active');
				$('tr.entryBlock').remove();

			} else {

				$('tr.week td').removeClass('active');
				$thisDate.addClass('active');

				$('tr.entryBlock').remove();
				$thisRow.after('<tr class="entryBlock loading"><td colspan="'+colspanSetting+'"></td></tr>');
				$('tr.entryBlock').find('td').spin('booked');

				booked_load_calendar_date_booking_options = {'load':'calendar_date','date':date,'calendar_id':calendar_id};

				/*
				 * allow other script to apply their modification before loading the booking options
				 * $(document).on("booked-before-loading-calendar-booking-options", function(event, params) { code goes here });
				 */
				$(document).trigger("booked-before-loading-calendar-booking-options");

				$('tr.entryBlock').find('td').load(booked_ajaxURL, booked_load_calendar_date_booking_options, function(){
					$('tr.entryBlock').removeClass('loading');
					$('tr.entryBlock').find('.booked-appt-list').fadeIn(300);
					$('tr.entryBlock').find('.booked-appt-list').addClass('shown');
					adjust_calendar_boxes();
				});

			}

			adjust_calendar_boxes();
			return false;

		});

		// New Appointment Click
		$('.booked-calendar-wrap').on('click', 'tr.entryBlock button.new-appt', function(e) {

			e.preventDefault();

			var $button 		= $(this),
				timeslot		= $button.attr('data-timeslot'),
				date			= $button.attr('data-date'),
				$thisTimeslot	= $button.parents('.timeslot'),
				booked_ajaxURL  = $('#data-ajax-url').html(),
				calendar_id		= $button.parents('table.booked-calendar').attr('data-calendar-id');

			booked_load_calendar_date_booking_options = {'load':'new_appointment_form','date':date,'timeslot':timeslot,'calendar_id':calendar_id};

			/*
			 * $(document).on("booked-before-loading-calendar-booking-options", function(event, params) { code goes here });
			 */
			$(document).trigger("booked-before-loading-calendar-booking-options");

			create_booked_modal();
			$('.bm-window').load(booked_ajaxURL, booked_load_calendar_date_booking_options, function (responseText) {
				/*
				 * allow other script to apply modification on content loaded
				 * $(document).on("booked-on-new-app", function(event) { code goes here });
				 */
				$(document).trigger("booked-on-new-app");
			});

			return false;

		});

		// Profile Tabs
		var profileTabs = $('.booked-tabs');

		if (!profileTabs.find('li.active').length){
			profileTabs.find('li:first-child').addClass("active");
		}

		if (profileTabs.length){
			$('.booked-tab-content').hide();
			var activeTab = profileTabs.find('.active > a').attr('href');
			activeTab = activeTab.split('#');
			activeTab = activeTab[1];
			$('#profile-'+activeTab).show();

			profileTabs.find('li > a').on('click', function(e) {

				e.preventDefault();
				$('.booked-tab-content').hide();
				profileTabs.find('li').removeClass('active');

				$(this).parent().addClass('active');
				var activeTab = $(this).attr('href');
				activeTab = activeTab.split('#');
				activeTab = activeTab[1];

				$('#profile-'+activeTab).show();
				return false;

			});
		}

		// Show Additional Information
		$('.booked-profile-appt-list').on('click', '.booked-show-cf', function(e) {
		
			e.preventDefault();
			var hiddenBlock = $(this).parent().find('.cf-meta-values-hidden');
		
			if(hiddenBlock.is(':visible')){
				hiddenBlock.hide();
			} else {
				hiddenBlock.show();
			}
		
			return false;
		
		});

		// Check Login/Registration/Forgot Password forms before Submitting
		if ($('#loginform').length){
			$('#loginform input[type="submit"]').on('click',function(e) {
				if ($('#loginform input[name="log"]').val() && $('#loginform input[name="pwd"]').val()){
					$('#loginform .booked-custom-error').hide();
				} else {
					e.preventDefault();
					$('#loginform').parents('.booked-form-wrap').find('.booked-custom-error').fadeOut(200).fadeIn(200);
				}
			});
		}

		if ($('#profile-forgot').length){
			$('#profile-forgot input[type="submit"]').on('click',function(e) {
				if ($('#profile-forgot input[name="user_login"]').val()){
					$('#profile-forgot .booked-custom-error').hide();
				} else {
					e.preventDefault();
					$('#profile-forgot').find('.booked-custom-error').fadeOut(200).fadeIn(200);
				}
			});
		}

		// Custom Upload Field
		if ($('.booked-upload-wrap').length){

			$('.booked-upload-wrap input[type=file]').on('change',function(){

				var fileName = $(this).val();
				$(this).parent().find('span').html(fileName);
				$(this).parent().addClass('hasFile');

			});

		}

		// Delete Appointment from Pending List
		$('.booked-profile-appt-list').on('click', '.appt-block .cancel', function(e) {

			e.preventDefault();

			var $button 		= $(this),
				$thisParent		= $button.parents('.appt-block'),
				appt_id			= $thisParent.attr('data-appt-id'),
				booked_ajaxURL  = $('#data-ajax-url').html();

			confirm_delete = confirm(i18n_confirm_appt_delete);
			if (confirm_delete == true){

				var currentApptCount = parseInt($('.booked-profile-appt-list').find('h4').find('span.count').html());
				currentApptCount = parseInt(currentApptCount - 1);
				if (currentApptCount < 1){
					$('.booked-profile-appt-list').find('h4').find('span.count').html('0');
					$('.no-appts-message').slideDown('fast');
				} else {
					$('.booked-profile-appt-list').find('h4').find('span.count').html(currentApptCount);
				}
				
				$('.appt-block').animate({'opacity':0.4},0);

	  			$thisParent.slideUp('fast',function(){
					$(this).remove();
				});

				ajaxRequests.push = $.ajaxQueue({
					'method' : 'POST',
					'url' : booked_ajaxURL,
					'data': {
						'action'     	: 'cancel_appt',
						'appt_id'     	: appt_id
					},
					success: function(data) {
						$('.appt-block').animate({'opacity':1},150);
					}
				});

			}

			return false;

		});

		$('body').on('click','.bm-overlay, .bm-window .close, .booked-form .cancel',function(e){
			e.preventDefault();
			close_booked_modal();
			return false;
		});

		$('body')
		.on('focusin', '.booked-form input', function() {
			if(this.title==this.value) {
				$(this).addClass('hasContent');
				this.value = '';
			}
		}).on('focusout', '.booked-form input', function(){
			if(this.value==='') {
				$(this).removeClass('hasContent');
				this.value = this.title;
			}
		});

		$('body').on('change','.booked-form input',function(){

			var condition = $(this).attr('data-condition'),
				thisVal = $(this).val();

			if (condition && $('.condition-block').length) {
				$('.condition-block.'+condition).hide();
				$('#condition-'+thisVal).fadeIn(200);
			}

		});

		// Perform AJAX login on form submit
	    $('body').on('submit','form#ajaxlogin', function(e){
		    e.preventDefault();

		    var booked_ajaxURL  = $('#data-ajax-url').html();
	        $('form#ajaxlogin p.status').show().html(i18n_please_wait);

	        ajaxRequests.push = $.ajaxQueue({
		        type	: 'post',
				url 	: booked_ajaxURL,
				data	: $('form#ajaxlogin').serialize(),
				success	: function(data) {
					if (data == 'success'){

						var	timeslot		= $('#newAppointmentForm input[name=timeslot]').val(),
							date			= $('#newAppointmentForm input[name=date]').val(),
							booked_ajaxURL  = $('#data-ajax-url').html(),
							calendar_id		= $('#newAppointmentForm').attr('data-calendar-id');

						$('.bm-window').load(booked_ajaxURL, {'load':'new_appointment_form','date':date,'timeslot':timeslot,'calendar_id':calendar_id});
						return false;

					} else {
						$('form#ajaxlogin p.status').show().html(i18n_wrong_username_pass);
					}
	            }
	        });
	        e.preventDefault();
	    });

		$('body').on('click','.booked-form input#submit-request-appointment',function(e){
			e.preventDefault();
			
			var customerType 		= $('#newAppointmentForm input[name=customer_type]').val(),
				customerID			= $('#newAppointmentForm input[name=user_id]').val(),
				firstName			= $('#newAppointmentForm input[name=first_name]').val(),
				firstNameDefault	= $('#newAppointmentForm input[name=first_name]').attr('title'),
				lastName			= $('#newAppointmentForm input[name=last_name]').val(),
				email				= $('#newAppointmentForm input[name=email]').val(),
				emailDefault		= $('#newAppointmentForm input[name=email]').attr('title'),
				phone				= $('#newAppointmentForm input[name=phone]').val(),
				mobile				= $('#newAppointmentForm input[name=mobile]').val(),
				calendar_id			= $('#newAppointmentForm').attr('data-calendar-id'),
				showRequiredError	= false,
				ajaxRequests 		= [];

			$(this).parents('.booked-form').find('input,textarea,select').each(function(i,field){

				var required = $(this).attr('required');

				if (required && $(field).attr('type') == 'hidden'){
					var fieldParts = $(field).attr('name');
					fieldParts = fieldParts.split('---');
					fieldName = fieldParts[0];
					fieldNumber = fieldParts[1].split('___');
					fieldNumber = fieldNumber[0];

					if (fieldName == 'radio-buttons-label'){
						var radioValue = false;
						$('input:radio[name="single-radio-button---'+fieldNumber+'[]"]:checked').each(function(){
							if ($(this).val()){
								radioValue = $(this).val();
							}
						});
						if (!radioValue){
							showRequiredError = true;
						}
					} else if (fieldName == 'checkboxes-label'){
						var checkboxValue = false;
						$('input:checkbox[name="single-checkbox---'+fieldNumber+'[]"]:checked').each(function(){
							if ($(this).val()){
								checkboxValue = $(this).val();
							}
						});
						if (!checkboxValue){
							showRequiredError = true;
						}
					}

				} else if (required && $(field).attr('type') != 'hidden' && $(field).val() == ''){
		            showRequiredError = true;
		        }

		    });

		    if (showRequiredError){
			    alert(i18n_fill_out_required_fields);
			    return false;
		    }

			if (customerType == 'current' && customerID || customerType == 'guest'){

				$thisButton = $(this);
				$thisButton.val(i18n_please_wait).attr('disabled',true).after('<span class="temp-loading-spinner">&nbsp;&nbsp;<i style="font-size:25px; color:#aaa; position:relative; top:5px;" class="fa fa-refresh fa-spin"></i></span>');
				$thisButton.parents('form').find('button.cancel').hide();

				$('.booked-form input').each(function(){
					thisDefault = $(this).attr('title');
					thisVal = $(this).val();
					if (thisDefault == thisVal){ $(this).val(''); }
				});

				var booked_ajaxURL	= $('#data-ajax-url').html(),
					$activeTD		= $('td.active');

				ajaxRequests.push = $.ajaxQueue({
					type	: 'post',
					url 	: booked_ajaxURL,
					data	: $('#newAppointmentForm').serialize(),
					success	: function(data) {

						data = data.split('###');
						var data_result = data[0].substr(data[0].length - 5);

						if (data_result == 'error'){

							$thisButton.val(i18n_request_appointment).attr('disabled',false);
							$thisButton.parents('form').find('button.cancel').show();
							$('.temp-loading-spinner').remove();

							$('.booked-form input').each(function(){
								thisDefault = $(this).attr('title');
								thisVal = $(this).val();
								if (!thisVal){ $(this).val(thisDefault); }
							});

							alert(data[1]);

						} else {
							
							$(document).trigger("booked-on-requested-appointment");
							if (profilePage){
								window.location = profilePage + '?appt_requested';
							}

						}

					}
				});

				return false;

			}

			if (customerType == 'new' && firstName != firstNameDefault && email != emailDefault){

				$('.booked-form input').each(function(){
					thisDefault = $(this).attr('title');
					thisVal = $(this).val();
					if (thisDefault == thisVal){ $(this).val(''); }
				});

				$thisButton = $(this);

				$thisButton.val(i18n_please_wait).attr('disabled',true).after('<span class="temp-loading-spinner">&nbsp;&nbsp;<i style="font-size:25px; color:#aaa; position:relative; top:5px;" class="fa fa-refresh fa-spin"></i></span>');
				$thisButton.parents('form').find('button.cancel').hide();

				var booked_ajaxURL	= $('#data-ajax-url').html(),
					$activeTD		= $('td.active');

				ajaxRequests.push = $.ajaxQueue({
					type	: 'post',
					url 	: booked_ajaxURL,
					data 	: $('#newAppointmentForm').serialize(),
					success: function(data) {

						data = data.split('###');
						var data_result = data[0].substr(data[0].length - 5);

						if (data_result == 'error'){

							$thisButton.val(i18n_request_appointment).attr('disabled',false);
							$thisButton.parents('form').find('button.cancel').show();
							$('.temp-loading-spinner').remove();

							$('.booked-form input').each(function(){
								thisDefault = $(this).attr('title');
								thisVal = $(this).val();
								if (!thisVal){ $(this).val(thisDefault); }
							});

							alert(data[1]);

						} else {

							/*
							 * $(document).on("booked-on-requested-appointment", function(event) { code goes here });
							 */
							$(document).trigger("booked-on-requested-appointment");

							$('tr.entryBlock').find('td').load(booked_ajaxURL, {'load':'calendar_date','date':data[1],'calendar_id':calendar_id},function(){
								$('tr.entryBlock').find('.booked-appt-list').show();
								$('tr.entryBlock').find('.booked-appt-list').addClass('shown');
							});
							
							$activeTD.load(booked_ajaxURL, {'load':'refresh_date_square','date':data[1],'calendar_id':calendar_id},function(){
								var self = $(this);
								self.replaceWith(self.children());
								adjust_calendar_boxes();
								if (profilePage){
									window.location = profilePage + '?appt_requested&new_account';
								} else {
									close_booked_modal();
								}
							});

						}
					}
				});

				return false;

			} else if (customerType == 'new' && firstName == firstNameDefault || customerType == 'new' && email == emailDefault){

				alert(i18n_appt_required_fields);

			}

		});

		// Adjust the calendar sizing when resizing the window
		$win.resize(function(){
			adjust_calendar_boxes();
			resize_booked_modal();
		});

		// Adjust the calendar sizing on load
		adjust_calendar_boxes();

	});



	// Create Booked Modal
	function create_booked_modal(){
		var windowHeight = $(window).height();
		var windowWidth = $(window).width();
		if (windowWidth > 720){
			var maxModalHeight = windowHeight - 295;
		} else {
			var maxModalHeight = windowHeight;
		}
		$('body *').blur();
		$('body').css({'overflow':'hidden'});
		$('<div class="booked-modal"><div class="bm-overlay"></div><div class="bm-window"><div style="height:100px"></div></div></div>').appendTo('body');
		$('.booked-modal .bm-window').spin('booked');
		$('.booked-modal .bm-window').css({'max-height':maxModalHeight+'px'});
	}
	
	function resize_booked_modal(){
		var windowHeight = $(window).height();
		var windowWidth = $(window).width();
		if (windowWidth > 720){
			var maxModalHeight = windowHeight - 295;
		} else {
			var maxModalHeight = windowHeight;
		}
		$('.booked-modal .bm-window').css({'max-height':maxModalHeight+'px'});
	}

	// Saving state updater
	function savingState(show,limit_to){

		show = typeof show !== 'undefined' ? show : true;
		limit_to = typeof limit_to !== 'undefined' ? limit_to : false;

		if (limit_to){

			var $savingStateDIV = limit_to.find('li.active .savingState, .topSavingState.savingState, .calendarSavingState');
			var $stuffToHide = limit_to.find('.monthName');
			var $stuffToTransparent = limit_to.find('table.booked-calendar tbody');

		} else {

			var $savingStateDIV = $('li.active .savingState, .topSavingState.savingState, .calendarSavingState');
			var $stuffToHide = $('.monthName');
			var $stuffToTransparent = $('table.booked-calendar tbody');

		}

		if (show){
			$savingStateDIV.fadeIn(200);
			$stuffToHide.hide();
			$stuffToTransparent.animate({'opacity':0.2},100);
		} else {
			$savingStateDIV.hide();
			$stuffToHide.show();
			$stuffToTransparent.animate({'opacity':1},0);
		}

	}

	$(document).ajaxStop(function() {
		savingState(false);
	});

})(jQuery, window, document);

function close_booked_modal(){
	jQuery('.booked-modal').fadeOut(200);
	jQuery('.booked-modal').addClass('bm-closing');
	jQuery('body').css({'overflow':'auto'});
	setTimeout(function(){
		jQuery('.booked-modal').remove();
	},300);
}

// Function to adjust calendar sizing
function adjust_calendar_boxes(){
	jQuery('.booked-calendar').each(function(){
		var boxesWidth = jQuery(this).find('tbody tr.week td').width();
		var calendarHeight = jQuery(this).height();
		boxesHeight = boxesWidth * 0.8;
		jQuery(this).find('tbody tr.week td').height(boxesHeight);
		jQuery(this).find('tbody tr.week td .date').css('line-height',boxesHeight+'px');

		var calendarHeight = jQuery(this).height();
		jQuery(this).parent().height(calendarHeight);
		
		jQuery('.tooltipster').tooltipster({
			theme: 'tooltipster-light',
			delay: 500,
		});
	});
}

// Ajax Queue Function
(function($) {

	// jQuery on an empty object, we are going to use this as our Queue
	var ajaxQueue = $({});

	$.ajaxQueue = function( ajaxOpts ) {
	    var jqXHR,
	        dfd = $.Deferred(),
	        promise = dfd.promise();

	    // queue our ajax request
	    ajaxQueue.queue( doRequest );

	    // add the abort method
	    promise.abort = function( statusText ) {

	        // proxy abort to the jqXHR if it is active
	        if ( jqXHR ) {
	            return jqXHR.abort( statusText );
	        }

	        // if there wasn't already a jqXHR we need to remove from queue
	        var queue = ajaxQueue.queue(),
	            index = $.inArray( doRequest, queue );

	        if ( index > -1 ) {
	            queue.splice( index, 1 );
	        }

	        // and then reject the deferred
	        dfd.rejectWith( ajaxOpts.context || ajaxOpts, [ promise, statusText, "" ] );
	        return promise;
	    };

	    // run the actual query
	    function doRequest( next ) {
	        jqXHR = $.ajax( ajaxOpts )
	            .done( dfd.resolve )
	            .fail( dfd.reject )
	            .then( next, next );
	    }

	    return promise;
	};

})(jQuery);