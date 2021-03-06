link_flags :=
compile_flags := -O2 -std=c++11
cov_link_flags := $(link_flags) --coverage
cov_compile_flags := --coverage -std=c++11

main := main
tester := testing/tester
bin_path := bin/
cov_bin_path := bin/cov/

sources := $(main).cpp $(tester).cpp splaytree/splay_tree.cpp linkcut/link_cut_tree.cpp
objects := $(addprefix $(bin_path), $(sources:.cpp=.o))
depends := $(addprefix $(bin_path), $(sources:.cpp=.d))
cov_objects := $(addprefix $(cov_bin_path), $(sources:.cpp=.o))
cov_depends := $(addprefix $(cov_bin_path), $(sources:.cpp=.d))

$(bin_path)$(main) : $(filter-out $(bin_path)$(tester).o, $(objects))
	@mkdir -p $(dir $@)
	g++ $(compile_flags) $^ -o $@ $(link_flags)

$(bin_path)$(tester) : $(filter-out $(bin_path)$(main).o, $(objects))
	@mkdir -p $(dir $@)
	g++ $(compile_flags) $^ -o $@ $(link_flags)

$(cov_bin_path)$(tester) : $(filter-out $(cov_bin_path)$(main).o, $(cov_objects))
	@mkdir -p $(dir $@)
	g++ $(cov_compile_flags) $^ -o $@ $(cov_link_flags)

-include $(depends)
-include $(cov_depends)

$(bin_path)%.o : %.cpp
	@mkdir -p $(dir $@)
	g++ $(compile_flags) -c -MMD $< -o $@

$(cov_bin_path)%.o : %.cpp
	@mkdir -p $(dir $@)
	g++ $(cov_compile_flags) -c -MMD $< -o $@

.PHONY : test
test : $(bin_path)$(tester)
	@./$(bin_path)$(tester)

.PHONY : run
run : $(bin_path)$(main)
	@./$(bin_path)$(main)

.PHONY : coverage
coverage : $(cov_bin_path)$(tester)
	@lcov -q -o $(cov_bin_path)initcovdata.info -c -i -d $(cov_bin_path) -b $(cov_bin_path) 2>/dev/null
	@./$(cov_bin_path)$(tester)
	@gcov $(cov_bin_path)$(tester) -r >/dev/null
	@lcov -q -o $(cov_bin_path)runcovdata.info -c -d $(cov_bin_path) -b $(cov_bin_path) 2>/dev/null
	@lcov -q -a $(cov_bin_path)initcovdata.info -a $(cov_bin_path)runcovdata.info -o $(cov_bin_path)rescovdata.info 2>/dev/null
	@lcov -q -e $(cov_bin_path)rescovdata.info "`pwd`/*" -o $(cov_bin_path)rescovdata.info 2>/dev/null
	@genhtml -q -o $(cov_bin_path)html/ $(cov_bin_path)rescovdata.info 2>/dev/null
	@echo created coverage report index.html in bin/cov/html/index.html

.PHONY : clean
clean :
	@rm -rf ./bin
