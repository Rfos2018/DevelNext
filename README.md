<?php


			return NULL;
		}

		global $APPLICATION;

		if ($value == NULL) {
			return $APPLICATION->title;
		}
		else {
			$APPLICATION->title = $value;
		}
	}
}

class Logger
{
	public function msg($text, $format, $prefix = "")
	{
		c("richEdit1")->fontColor;
	}
}

class methodEngine
{
	static 	public $DATA;
	static 	public $form;

	static public function dataToLower()
	{
		foreach (self::$DATA as $form => $obj ) {
			if (strtolower($form) != $form) {
				self::$DATA[strtolower($form)] = self::$DATA[$form];
				unset(self::$DATA[$form]);
			}
		}

		foreach (self::$DATA as $form => $objs ) {
			foreach ($objs as $obj => $list ) {
				if (strtolower($obj) != $obj) {
					self::$DATA[$form][strtolower($obj)] = self::$DATA[$form][$obj];
					unset(self::$DATA[$form][$obj]);
				}
				else if (strtolower($obj) == "fmedit") {
					self::$DATA[$form]["--fmedit"] = self::$DATA[$form][$obj];
					unset(self::$DATA[$form][$obj]);
				}
			}
		}
	}

	static public function setForm($form = false)
	{
		global $_FORMS;
		global $formSelected;

		if ($form) {
			self::$form = strtolower($form);
		}
		else {
			self::$form = strtolower($_FORMS[$formSelected]);
		}
	}

	static public function getMethod($object, $type)
	{
		$type = strtolower($type);
		return self::$DATA[strtolower(self::$form)][strtolower($object)][strtolower($type)];
	}

	static public function setMethod($object, $type, $value)
	{
		$type = strtolower($type);
		self::$DATA[strtolower(self::$form)][strtolower($object)][strtolower($type)] = $value;
	}

	static public function copyMethod($original, $new, $type = false)
	{
		$type = strtolower($type);
		$new = strtolower($new);
		$original = strtolower($original);

		if ($type) {
			self::$DATA[self::$form][$new][$type] = self::$DATA[self::$form][$original][$type];
		}
		else {
			self::$DATA[self::$form][$new] = self::$DATA[self::$form][$original];
		}
	}

	static public function changeMethod($object, $type, $new)
	{
		$type = strtolower($type);
		$new = strtolower($new);
		$object = strtolower($object);
		self::$DATA[self::$form][$object][$new] = self::$DATA[self::$form][$object][$type];
		unset(self::$DATA[self::$form][$object][$type]);
	}

	static public function delMethod($object, $type = false)
	{
		$type = strtolower($type);
		$object = strtolower($object);

		if ($type) {
			unset(self::$DATA[self::$form][$object][$type]);
		}
		else {
			unset(self::$DATA[self::$form][$object]);
		}
	}

	static public function changeName($object, $new)
	{
		if ($object == $new) {
			return NULL;
		}

		self::copyMethod($object, $new);
		self::delMethod($object);
	}

	static public function methodList($object)
	{
		return array_keys((array) self::$DATA[strtolower(self::$form)][strtolower($object)]);
	}

	static public function listMethods($obj)
	{
		return self::methodList($obj);
	}

	static public function listMethodsEx($obj)
	{
		$result = array();
		$methods = self::listMethods($obj);

		foreach ($methods as $i => $ev ) {
			$result[] = t($ev);
		}

		return $result;
	}

	static public function updateIndexes()
	{
		global $fmEdit;
		self::$DATA["--indexes"][self::$form] = array();

		foreach ((array) self::$DATA[self::$form] as $obj_name => $code ) {
			if ($obj_name == "--fmedit") {
				self::$DATA["--indexes"][self::$form][$obj_name] = -1;
			}
			else {
				self::$DATA["--indexes"][self::$form][$obj_name] = $fmEdit->findComponent($obj_name)->componentIndex;
			}
		}
	}

	static public function updateIndex($obj)
	{
		self::$DATA["--indexes"][_c($obj->owner)->name][$obj->name] = $obj->componentIndex;
	}
}

class TThumbButton
{
	public $dwMask;
	public $iId;
	public $iBitmap;
	public $Icon;
	public $szTip;
	public $dwFlags;
}

class TThumbButtonArray
{
	public $Value = array();

	public function add(TThumbButton $elm1, TThumbButton $elm2 = NULL, TThumbButton $elm3 = NULL, TThumbButton $elm4 = NULL, TThumbButton $elm5 = NULL, TThumbButton $elm6 = NULL, TThumbButton $elm7 = NULL)
	{
		if (7 <= count($this->Value)) {
			return false;
		}

		foreach (func_get_args() as $v ) {
			if (7 <= count($this->Value)) {
				break;
			}

			$this->Value[] = $v;
		}

		return true;
	}

	public function Rem($idx)
	{
		if (array_key_exists($idx, $this->Value)) {
			unset($this->Value[$idx]);
			return false;
		}

		return false;
	}

	public function GetElm($idx)
	{
		if (array_key_exists($idx, $this->Value)) {
			return $this->Value[$idx];
		}
		else {
			return false;
		}
	}

	public function ToArray()
	{
		$ListBtn = array();
		$i = 0;

		foreach ($this->Value as $v ) {
			if ($v->dwMask !== NULL) {
				$ListBtn[$i]["dwmask"] = $v->dwMask;
			}

			if ($v->iId !== NULL) {
				$ListBtn[$i]["iid"] = $v->iId;
			}

			if ($v->iBitmap !== NULL) {
				$ListBtn[$i]["ibitmap"] = $v->iBitmap;
			}

			if (is_file($v->Icon)) {
				$ListBtn[$i]["icon"] = $v->Icon;
			}

			if ($v->szTip !== NULL) {
				$ListBtn[$i]["sztip"] = $v->szTip;
			}

			if ($v->dwFlags !== NULL) {
				$ListBtn[$i]["dwflags"] = $v->dwFlags;
			}

			$i++;
		}

		return $ListBtn;
	}
}

class TWinTaskBar
{
	public $Self = 0;
	public $ApplicationHandle;
	public $RPosition = 0;
	public $RMaxPosition = 100;
	public $RProgressState = TBPF_NORMAL;

	public function __construct($ArrOpt = NULL, $ApplicationHandle = 0)
	{
		$this->Self = TWinTaskbarCreate();

		if (!$this->TBInitialize()) {
			throw new TWinTaskBarException($this->LastError());
			return NULL;
		}

		$this->ApplicationHandle = ($ApplicationHandle == 0 ? application_prop("handle", NULL) : $ApplicationHandle);
		$this->ProgressState = $this->RProgressState;
		$this->SetProgressValue($this->RPosition, $this->RMaxPosition);

		foreach ((array) $ArrOpt as $i => $v ) {
			$this->$i = $v;
		}
	}

	public function TBInitialize()
	{
		return $this->Self != 0 ? TBInitialize($this->Self) : false;
	}

	public function __set($name, $value)
	{
		switch (strtolower($name)) {
		case "position":
			$this->SetProgressValue((int) trim($value), $this->RMaxPosition);
			break;

		case "maxposition":
			$this->RMaxPosition = trim($value);
			break;

		case "progressstate":
			$this->SetProgressState($value);
			break;

		case "applicationhandle":
			$this->SetApplicationHandle = trim($value);
			break;

		case "onwmcommand":
			$this->OnWMCommand($value);
			break;

		case "progressstatetext":
			switch (strtolower(trim($value))) {
			case strtolower("TBPF_NOPROGRESS"):
				$this->progressstate = TBPF_NOPROGRESS;
				break;

			case :
				$this->progressstate = TBPF_INDETERMINATE;
				break;

			case :
				$this->progressstate = TBPF_NORMAL;
				break;

			case :
				$this->progressstate = TBPF_ERROR;
				break;

			case :
				$this->progressstate = TBPF_PAUSED;
				break;
			}

			break;
		}
	}

	public function __get($name)
	{
		switch (strtolower($name)) {
		case "position":
			return $this->RPosition;
			break;

		case "maxposition":
			return $this->RMaxPosition;
			break;

		case "progressstate":
			return $this->RProgressState;
			break;

		case "progressstatetext":
			switch ($this->ProgressState) {
			case TBPF_NOPROGRESS:
				return "TBPF_NOPROGRESS";
				break;

			case TBPF_INDETERMINATE:
				return "TBPF_INDETERMINATE";
				break;

			case TBPF_NORMAL:
				return "TBPF_NORMAL";
				break;

			case TBPF_ERROR:
				return "TBPF_ERROR";
				break;

			case TBPF_PAUSED:
				return "TBPF_PAUSED";
				break;
			}

			break;

		case "lasterror":
			return $this->LastError();
			break;

		case "applicationhandle":
			return $this->MainWindow();
			break;
		}
	}

	public function SetApplicationHandle($ApplicationHandle)
	{
		$this->ApplicationHandle = (is_numeric($ApplicationHandle) ? $ApplicationHandle : 0);
		$Result = ($this->TBInitialize() ? $this->MainWindow($this->ApplicationHandle) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function OnWMCommand($CallBackName)
	{
		$CallBackName = trim((string) $CallBackName);
		if (($CallBackName === NULL) || ($CallBackName == "")) {
			return $this->OnWMCommandUnregister();
		}
		else {
			if (!is_callable($CallBackName, true) || !function_exists($CallBackName)) {
				if (preg_match("/^[a-zA-Z_\\x7f-\\xff][a-zA-Z0-9_\\x7f-\\xff]*$/", $CallBackName) !== 1) {
					throw new TWinTaskBarException("Èìÿ ôóíêöèè \"" . $CallBackName . "\" äëÿ \"OnWMCommand\" çàäàíî íåâåðíî");
				}
				else {
					throw new TWinTaskBarException("Êîëáýê \"" . $CallBackName . "\" äëÿ \"OnWMCommand\" çàäàíî íåâåðíî");
				}

				return false;
			}
		}

		$Result = ($this->TBInitialize() ? TBWMCommandApplication($this->Self, $CallBackName, $this) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function OnWMCommandUnregister()
	{
		return $this->TBInitialize() ? TBUnregisterWMCommandApplication($this->Self) : false;
	}

	public function ActivateTab($AHwnd)
	{
		$Result = ($this->TBInitialize() ? TBActivateTab($this->Self, $AHwnd) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function AddTab($AHwnd)
	{
		$Result = ($this->TBInitialize() ? TBAddTab($this->Self, $AHwnd) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function DeleteTab($AHwnd)
	{
		$Result = ($this->TBInitialize() ? TBDeleteTab($this->Self, $AHwnd) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function SetActiveAlt($AHwnd)
	{
		$Result = ($this->TBInitialize() ? TBSetActiveAlt($this->Self, $AHwnd) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function MarkFullscreenWindow($AHwnd, $AFullscreen)
	{
		$Result = ($this->TBInitialize() ? TBMarkFullscreenWindow($this->Self, $AFullscreen) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function RegisterTab($ATabHandle)
	{
		$Result = ($this->TBInitialize() ? TBRegisterTab($this->Self, $ATabHandle) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function SetOverlayIcon($AIcon, $ADescription)
	{
		$Result = ($this->TBInitialize() ? TBSetOverlayIcon($this->Self, $AIcon, $this->MainWindow()) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function SetProgressState($AState)
	{
		if ($this->TBInitialize()) {
			if (!TBSetProgressState($this->Self, $this->MainWindow(), TBPF_NOPROGRESS)) {
				throw new TWinTaskBarException($this->LastError());
				return false;
			}
			else if ($AState == TBPF_NOPROGRESS) {
				$this->RProgressState = TBPF_NOPROGRESS;
				$this->RPosition = 0;
				return true;
			}
			else {
				if (!TBSetProgressState($this->Self, $this->MainWindow(), $AState)) {
					throw new TWinTaskBarException($this->LastError());
					return false;
				}

				$this->RProgressState = $AState;

				if ($AState != TBPF_INDETERMINATE) {
					if (!$this->SetProgressValue($this->RPosition, $this->RMaxPosition)) {
						throw new TWinTaskBarException($this->LastError());
					}
				}
				else {
					$this->RPosition = 0;
				}

				return true;
			}
		}
		else {
			return false;
		}
	}

	public function SetProgressValue($ACompleted, $ATotal)
	{
		if ($this->TBInitialize()) {
			if ($this->RMaxPosition < $ACompleted) {
				while (!$this->RMaxPosition == $ACompleted) {
					--$ACompleted;
				}
			}

			if (($ACompleted < 0) && (0 < ($this->RPosition - $ACompleted))) {
				$ACompleted = 0;
			}

			$this->RPosition = $ACompleted;
			$Result = TBSetProgressValue((int) $this->Self, (int) $this->MainWindow(), (int) $ACompleted, (int) $ATotal);

			if (!$Result) {
				throw new TWinTaskBarException($this->LastError());
			}
			else {
				if (($this->RProgressState == TBPF_NOPROGRESS) || ($this->RProgressState == TBPF_INDETERMINATE)) {
					$this->RProgressState = TBPF_NORMAL;
				}
			}

			return $Result;
		}
		else {
			return false;
		}
	}

	public function SetTabActive($AHwndTab)
	{
		$Result = ($this->TBInitialize() ? TBSetTabActive($this->Self, $AHwndTab) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function SetTabOrder($AHwndTab, $AHwndInsertBefore = 0)
	{
		$Result = ($this->TBInitialize() ? TBSetTabOrder($this->Self, $AHwndInsertBefore) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function SetThumbnailClip(TRect $AClipRect)
	{
		if ($this->TBInitialize()) {
			$arrAClipRect = array();

			if ($AClipRect->Left !== NULL) {
				$arrAClipRect["left"] = $AClipRect->Left;
			}

			if ($AClipRect->Top !== NULL) {
				$arrAClipRect["top"] = $AClipRect->Top;
			}

			if ($AClipRect->Right !== NULL) {
				$arrAClipRect["right"] = $AClipRect->Right;
			}

			if ($AClipRect->Bottom !== NULL) {
				$arrAClipRect["bottom"] = $AClipRect->Bottom;
			}

			$Result = TBSetThumbnailClip($this->Self, $this->MainWindow(), $arrAClipRect);

			if (!$Result) {
				throw new TWinTaskBarException($this->LastError());
			}

			return $Result;
		}
		else {
			return false;
		}
	}

	public function ClearThumbnailClip()
	{
		$Result = ($this->TBInitialize() ? TBClearThumbnailClip($this->Self, $this->MainWindow()) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function SetThumbnailTooltip($ATip)
	{
		$Result = ($this->TBInitialize() ? TBSetThumbnailTooltip($this->Self, $this->MainWindow(), $ATip) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function ClearThumbnailTooltip()
	{
		$Result = ($this->TBInitialize() ? TBClearThumbnailTooltip($this->Self) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function ThumbBarAddButtons(TThumbButtonArray $AButtonList)
	{
		if ($this->TBInitialize() && !empty($AButtonList->Value)) {
			$Result = TBThumbBarAddButtons($this->Self, $AButtonList->ToArray(), $this->MainWindow());

			if (!$Result) {
				throw new TWinTaskBarException($this->LastError());
			}

			return $Result;
		}
		else {
			return false;
		}
	}

	public function ThumbBarSetImageList($AImageList)
	{
		$Result = ($this->TBInitialize() ? TBThumbBarSetImageList($this->Self, $this->MainWindow(), $AImageList) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function ThumbBarUpdateButtons(TThumbButtonArray $AButtonList)
	{
		if ($this->TBInitialize() && !empty($AButtonList->Value)) {
			$Result = TBThumbBarUpdateButtons($this->Self, $AButtonList->ToArray(), $this->MainWindow());

			if (!$Result) {
				throw new TWinTaskBarException($this->LastError());
			}

			return $Result;
		}
		else {
			return false;
		}
	}

	public function UnregisterTab($AHwndTab)
	{
		$Result = ($this->TBInitialize() ? TBUnregisterTab($this->Self, $AHwndTab) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function SetTabProperties($AHwndTab, $AStpFlags)
	{
		$Result = ($this->TBInitialize() ? TBSetTabProperties($this->Self, $AHwndTab, $AStpFlags) : false);

		if (!$Result) {
			throw new TWinTaskBarException($this->LastError());
		}

		return $Result;
	}

	public function LastError()
	{
		return $this->TBInitialize() ? trim(TBLastError($this->Self)) : false;
	}

	public function MainWindow($handle = NULL)
	{
		if ($this->TBInitialize()) {
			if ($handle === NULL) {
				return TBMainWindow($this->Self);
			}
			else {
				return TBMainWindow($this->Self, $handle);
			}
		}
		else {
			return false;
		}
	}
}

class t
{
	static 	private $block = array();
	private $name;

	public function __construct($name)
	{
		$this->name = $name;
	}

	static public function c($name, $block = false)
	{
		if ($key = $GLOBALS["THREAD_SELF"]) {
			if ($block) {
				$block = ($block < 10 ? 10 : $block) * 1000;

				while (SyncEx("t::block", array($name, $key)) != "true") {
					usleep($block);
				}
			}

			if (empty($GLOBALS["___t_self"][$name])) {
				$GLOBALS["___t_self"][$name] = new t($name);
			}

			return $GLOBALS["___t_self"][$name];
		}
		else {
			if (!isset($GLOBALS["___t_objects"][$name])) {
				$GLOBALS["___t_objects"][$name] = c($name);
			}

			return $GLOBALS["___t_objects"][$name];
		}
	}

	static public function unBlock($name)
	{
		self::$block[$name] = NULL;
	}

	static public function block($name, $id)
	{
		if (!empty(self::$block[$name]) && (self::$block[$name] != $id)) {
			return "false";
		}

		self::$block[$name] = $id;
		return "true";
	}

	public function __get($name)
	{
		return SyncEx("t::get", array($this->name, $name));
	}

	static public function get($name, $property)
	{
		if (!isset($GLOBALS["___t_objects"][$name])) {
			$GLOBALS["___t_objects"][$name] = c($name);
		}

		$GLOBALS["APPLICATION"]->ProcessMessages();
		return $GLOBALS["___t_objects"][$name]->$property;
	}

	public function __set($name, $value)
	{
		Sync("t::set", array($this->name, $name, $value));
	}

	static public function set($name, $property, $value)
	{
		if (!isset($GLOBALS["___t_objects"][$name])) {
			$GLOBALS["___t_objects"][$name] = c($name);
		}

		$GLOBALS["___t_objects"][$name]->$property = $value;
		$GLOBALS["APPLICATION"]->ProcessMessages();
	}

	public function __call($name, $args)
	{
		return SyncEx("t::call", array($this->name, $name, $args));
	}

	static public function call($name, $method, $args)
	{
		if (!isset($GLOBALS["___t_objects"][$name])) {
			$GLOBALS["___t_objects"][$name] = c($name);
		}

		$res = call_user_func_array(array($GLOBALS["___t_objects"][$name], $method), $args);
		$GLOBALS["APPLICATION"]->ProcessMessages();
		return $res;
	}
}

class CircleProgress
{
	public $bgColor = 16777215;
	public $bgpColor = 15;
	public $color = 16762377;
	public $thickness = 3;
	public $fontSize = 32;
	public $fontName = "Segoe UI Light";
	public $correction;
	public $radius;
	private $canvas;
	private $currentPos = 0;
	private $angle = 4.71;
	private $maxCounter = 6.27;
	private $plusAng = 0.005;
	private $correct;

	public function __construct(TMImage $control)
	{
		$this->canvas = new TControlCanvas();
		$this->canvas->control = $control;
		$this->radius = ($control->w / 2) - ($this->thickness * 2);
		$this->correction = $this->thickness;
		$this->position = 0;
		$control->h = $control->w;
	}

	public function __get($f)
	{
		$s = "get_" . $f;

		if (method_exists($this, $s)) {
			return $this->{$s}();
		}
		else if (property_exists($this, $f)) {
			return $this->$f;
		}
	}

	public function __set($f, $v)
	{
		$s = "set_" . $f;

		if (property_exists($this, $f)) {
			$this->$f = $v;
		}
		else if (method_exists($this, $s)) {
			$this->{$s}($v);
		}
	}

	private function get_position()
	{
		return $this->currentPos;
	}

	private function set_position($lvl)
	{
		if (!is_numeric($lvl)) {
			trigger_error("Only number!", E_USER_ERROR);
		}

		$this->canvas->pen->width = $this->thickness;
		$this->canvas->brush->color = $this->bgColor;
		$this->createBackground();

		if (0 < $lvl) {
			$max = $this->maxCounter;
			$max = ($max * $lvl) / 100;

			while ($counter < $max) {
				if (empty($angle) && empty($counter)) {
					$angle = $this->angle;
					$counter = 0;
				}

				$this->correct = $this->radius + $this->correction;
				$x = (cos($angle) * $this->radius) + $this->correct;
				$y = (sin($angle) * $this->radius) + $this->correct;
				$this->canvas->pen->color = $this->color;
				$this->canvas->moveTo($x, $y);
				$this->canvas->lineTo($x, $y);
				$angle += $this->plusAng;
				$counter += $this->plusAng;
			}
		}

		$this->constructProc($counter);
	}

	private function constructProc($counter)
	{
		$this->canvas->font->size = $this->fontSoze;
		$this->canvas->font->name = $this->fontName;
		$this->canvas->font->color = $this->color;
		$text = (0 < $counter ? round(($counter * 100) / $this->maxCounter) : 0);
		$this->currentPos = ($counter * 100) / $this->maxCounter;
		$proc = "%";
		$this->canvas->font->size = $this->fontSize / 2;
		$procWidth = $this->canvas->textWidth($proc);
		$procHeight = $this->canvas->textHeight($proc);
		$this->canvas->font->size = $this->fontSize;
		$textWidth = $this->canvas->textWidth($text);
		$textHeight = $this->canvas->textHeight($text);
		$this->canvas->textOut($this->correct - ($textWidth / 2) - ($procWidth / 2), $this->correct - ($textHeight / 2), $text);
		$this->canvas->font->size = $this->fontSize / 2;
		$this->canvas->textOut(($this->correct - ($textWidth / 2) - ($procWidth / 2)) + $textWidth, ($this->correct - ($textHeight / 2)) + ($textHeight - $procHeight), $proc);
	}

	private function createBackground()
	{
		$this->canvas->brush->color = $this->bgColor;
		$this->canvas->pen->color = $this->getColor();
		$this->canvas->clear();

		while ($counter < $this->maxCounter) {
			if (empty($angle) && empty($counter)) {
				$angle = $this->angle;
				$counter = 0;
			}

			$this->correct = $this->radius + $this->correction;
			$x = (cos($angle) * $this->radius) + $this->correct;
			$y = (sin($angle) * $this->radius) + $this->correct;
			$this->canvas->moveTo($x, $y);
			$this->canvas->lineTo($x, $y);
			$angle += $this->plusAng;
			$counter += $this->plusAng;
		}
	}

	private function getColor()
	{
		$color = $this->toRgb($this->bgColor);

		for ($rgb = 0; $rgb < 3; $rgb++) {
			$color[$rgb] -= $this->bgpColor;
		}

		$color = $this->toHex($color[0], $color[1], $color[2]);
		return $color;
	}

	private function toRgb($color)
	{
		return array($color & 255, ($color & 65280) >> 8, ($color & 16711680) >> 16);
	}

	private function toHex($r, $g, $b)
	{
		return ($b << 16) | ($g << 8) | $r;
	}
}

class resize
{
	static 	public $objects = array();
	static 	public $speed = 15;

	static public function resize_object($obj, $params = false)
	{
		if (!$params) {
			$params = array();
		}

		$pars = array("x", "y", "w", "h", "func", "speed", "time");
		$s = 7;

		for ($i = 0; $i < $s; ++$i) {
			if (!isset($params[$pars[$i]])) {
				$params[$pars[$i]] = false;
			}
		}

		if ($params["time"] !== false) {
			$params["time"] += microtime(1);
		}

		$sides = array($params["x"], $params["y"], $params["w"], $params["h"]);
		resize::$objects[$obj->self] = array($sides, $params["func"], $params["speed"], $params["time"]);
	}

	static public function set_speed($i)
	{
		resize::$speed = $i;
	}

	static public function get_speed()
	{
		return resize::$speed;
	}

	static public function tick()
	{
		$objs = &resize::$objects;
		$size = count($objs);

		if (0 < $size) {
			$m = microtime(1);

			foreach ($objs as $self => $data ) {
				$time = $data[3];
				$speed = $data[2];
				$func = $data[1];
				$sides = $data[0];
				$sname = array("Left", "Top", "Width", "Height");
				$s = 4;

				if ($time !== false) {
					if ($m < $time) {
						continue;
					}
				}

				for ($i = 0; $i < $s; ++$i) {
					$sidetmp[$i] = gui_propGet($self, $sname[$i]);
					$remp[$i] = resize::eval_formule($sidetmp[$i], $sides[$i], $speed);
				}

				if (($remp[0] === false) && ($remp[1] === false) && ($remp[2] === false) && ($remp[3] === false)) {
					unset($objs[$self]);

					for ($i = 0; $i < $s; ++$i) {
						if ($sides[$i] !== false) {
							gui_propSet($self, $sides[$i], $sides[$i]);
						}
					}

					if (($func !== false) && is_callable($func)) {
						$func($self);
					}
				}
				else {
					for ($i = 0; $i < $s; ++$i) {
						if ($remp[$i] !== false) {
							gui_propSet($self, $sname[$i], $sidetmp[$i] + $remp[$i]);
						}
					}
				}
			}
		}
	}

	static public function syncfunc()
	{
	}

	static private function eval_formule($x, $y, $speed = false)
	{
		if (($x === false) || ($y === false)) {
			return false;
		}

		if ($speed === false) {
			$speed = resize::$speed;
		}

		$remp = (($y - $x) / 100) * resize::$speed;

		if ($remp == 0) {
			return false;
		}

		return 0 < $remp ? ceil($remp) : floor($remp);
	}
}

class WindowMove
{
	public function start($handle)
	{
		global $__j;
		global $APPLICATION;
		$__j = 1;
		$handle = c($handle);
		$sx = cursor_pos_x();
		$sy = cursor_pos_y();
		$fx = $handle->x;
		$fy = $handle->y;

		while ($__j != 0) {
			$APPLICATION->processMessages();
			$handle->x = $fx - $sx - cursor_pos_x();
			$handle->y = $fy - $sy - cursor_pos_y();
		}
	}

	public function stop($handle)
	{
		global $__j;
		$__j = 0;
		unset($__j);
	}
}

function setEchoController($obj_or_func)
{
	DSApi::echoController($obj_or_func);
}

?>

<?PHP

{
	$str = exemod_extractstr("\$RESLIST\$");
	$result = unserialize($str);

	foreach ($result as $i => $s ) {
		$result[$i] = str_replace("\$RES\$", "", $s);
	}

	return $result;
}

?>
