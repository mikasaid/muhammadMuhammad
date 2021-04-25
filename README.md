@@ -24,7 +24,7 @@ runners are supported:
|         **C#** | .NET                                                                                                               | `xunit`, `dotnettest`                                                                                                                        |	|         **C#** | .NET                                                                                                               | `xunit`, `dotnettest`                                                                                                                        |
|    **Clojure** | Fireplace.vim                                                                                                      | `fireplacetest`                                                                                                                              |	|    **Clojure** | Fireplace.vim                                                                                                      | `fireplacetest`                                                                                                                              |
|    **Crystal** | Crystal                                                                                                            | `crystalspec`                                                                                                                                |	|    **Crystal** | Crystal                                                                                                            | `crystalspec`                                                                                                                                |
|       **Dart** | Flutter Test, Pub Run Test                                                                                         | `fluttertest`, `pubruntest`                                                                                                                  |	|       **Dart** | Dart Test, Flutter Test                                                                                            | `darttest`, `fluttertest`
|     **Elixir** | ESpec, ExUnit                                                                                                      | `espec`, `exunit`                                                                                                                            |	|     **Elixir** | ESpec, ExUnit                                                                                                      | `espec`, `exunit`                                                                                                                            |
|        **Elm** | elm-test                                                                                                           | `elmtest`                                                                                                                                    |	|        **Elm** | elm-test                                                                                                           | `elmtest`                                                                                                                                    |
|     **Erlang** | CommonTest, EUnit                                                                                                  | `commontest`, `eunit`                                                                                                                        |	|     **Erlang** | CommonTest, EUnit                                                                                                  | `commontest`, `eunit`                                                                                                                        |
  20  autoload/test/dart/pubruntest.vim → autoload/test/dart/darttest.vim 
@@ -1,19 +1,19 @@
let g:test#dart#pubruntest#patterns = {	let g:test#dart#darttest#patterns = {
  \ 'test':      ['\v^\s*%(test)\(%(''|")(.*)%(''|"),'],	  \ 'test':      ['\v^\s*%(test)\(%(''|")(.*)%(''|"),'],
  \ 'namespace': ['\v^\s*group\(%(''|")(.*)%(''|"),'],	  \ 'namespace': ['\v^\s*group\(%(''|")(.*)%(''|"),'],
\}	\}


if !exists('g:test#dart#pubruntest#file_pattern')	if !exists('g:test#dart#darttest#file_pattern')
  let g:test#dart#pubruntest#file_pattern = '\v_test\.dart$'	  let g:test#dart#darttest#file_pattern = '\v_test\.dart$'
endif	endif


" Returns true if the given file belongs to your test runner	" Returns true if the given file belongs to your test runner
function! test#dart#pubruntest#test_file(file) abort	function! test#dart#darttest#test_file(file) abort
  return test#dart#test_file('pubruntest', g:test#dart#pubruntest#file_pattern, a:file)	  return test#dart#test_file('darttest', g:test#dart#darttest#file_pattern, a:file)
endfunction	endfunction


" Returns test runner's arguments which will run the current file and/or line	" Returns test runner's arguments which will run the current file and/or line
function! test#dart#pubruntest#build_position(type, position) abort	function! test#dart#darttest#build_position(type, position) abort
  if a:type ==# 'nearest'	  if a:type ==# 'nearest'
    let name = s:nearest_test(a:position)	    let name = s:nearest_test(a:position)
    if !empty(name)	    if !empty(name)
@@ -29,16 +29,16 @@ function! test#dart#pubruntest#build_position(type, position) abort
endfunction	endfunction


" Returns processed args (if you need to do any processing)	" Returns processed args (if you need to do any processing)
function! test#dart#pubruntest#build_args(args) abort	function! test#dart#darttest#build_args(args) abort
  return a:args	  return a:args
endfunction	endfunction


" Returns the executable of your test runner	" Returns the executable of your test runner
function! test#dart#pubruntest#executable() abort	function! test#dart#darttest#executable() abort
  return 'pub run test'	  return 'dart test'
endfunction	endfunction


function! s:nearest_test(position) abort	function! s:nearest_test(position) abort
  let name = test#base#nearest_test(a:position, g:test#dart#pubruntest#patterns)	  let name = test#base#nearest_test(a:position, g:test#dart#darttest#patterns)
  return join(name['namespace'] + name['test'], ' ')	  return join(name['namespace'] + name['test'], ' ')
endfunction	endfunction
  4  doc/test.txt 
@@ -267,8 +267,8 @@ In all commands [args] are forwarded to the underlying test runner.
                                                *test-:FlutterTest*	                                                *test-:FlutterTest*
:FlutterTest [args]  	         Uses the `flutter` `test` command.	:FlutterTest [args]  	         Uses the `flutter` `test` command.


                                                *test-:PubRunTest*	                                                *test-:DartTest*
:PubRunTest [args]  	         Uses the `pub run test` command.	:DartTest [args]  	         Uses the `dart test` command.


MAVEN TEST DIFFERENCES                          *test-maven-test-differences*	MAVEN TEST DIFFERENCES                          *test-maven-test-differences*


  2  plugin/test.vim 
@@ -9,7 +9,7 @@ let g:test#default_runners = {
  \ 'CSharp':     ['Xunit', 'DotnetTest'],	  \ 'CSharp':     ['Xunit', 'DotnetTest'],
  \ 'Clojure':    ['FireplaceTest'],	  \ 'Clojure':    ['FireplaceTest'],
  \ 'Crystal':    ['CrystalSpec'],	  \ 'Crystal':    ['CrystalSpec'],
  \ 'Dart':       ['PubRunTest', 'FlutterTest'],	  \ 'Dart':       ['DartTest', 'FlutterTest'],
  \ 'Elixir':     ['ExUnit', 'ESpec'],	  \ 'Elixir':     ['ExUnit', 'ESpec'],
  \ 'Elm':        ['ElmTest'],	  \ 'Elm':        ['ElmTest'],
  \ 'Erlang':     ['CommonTest', 'EUnit'],	  \ 'Erlang':     ['CommonTest', 'EUnit'],
  10  spec/dart_runner_spec.vim 
@@ -18,18 +18,18 @@ describe "Dart Runner"
      TestFile	      TestFile
      Expect g:test#last_command =~# '\v^flutter test'	      Expect g:test#last_command =~# '\v^flutter test'
    end	    end
    it "should use pub run test otherwise"	    it "should use dart test otherwise"
      view pubruntest/basic_test.dart	      view darttest/basic_test.dart
      TestFile	      TestFile
      Expect g:test#last_command =~# '\v^pub run test'	      Expect g:test#last_command =~# '\v^dart test'
    end	    end
  end	  end


  describe "when test#dart#runner is set"	  describe "when test#dart#runner is set"
    it "should respect test#dart#runner"	    it "should respect test#dart#runner"
      for runner in ["fluttertest", "pubruntest"]	      for runner in ["fluttertest", "darttest"]
        let g:test#dart#runner = runner	        let g:test#dart#runner = runner
        Expect test#determine_runner("pubruntest/basic_test.dart") == 'dart#'.runner	        Expect test#determine_runner("darttest/basic_test.dart") == 'dart#'.runner
        Expect test#determine_runner("fluttertest/widgets_test.dart") == 'dart#'.runner	        Expect test#determine_runner("fluttertest/widgets_test.dart") == 'dart#'.runner
      endfor	      endfor
    end	    end![download](https://user-images.githubusercontent.com/58392246/115999150-e2d37e00-a614-11eb-95a5-ddd1e86a640c.jpg)


