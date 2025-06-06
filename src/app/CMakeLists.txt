# Aseprite     | Copyright (C) 2001-2016  David Capello
# LibreSprite  | Copyright (C)      2021  LibreSprite contributors

# Generate a ui::Widget for each widget in a XML file
file(GLOB widget_files ${CMAKE_SOURCE_DIR}/data/widgets/*.xml)
foreach(widget_file ${widget_files})
  get_filename_component(widget_name ${widget_file} NAME_WE)
  set(output_fn ${CMAKE_CURRENT_BINARY_DIR}/${widget_name}.xml.h)

  add_custom_command(
    OUTPUT ${output_fn}
    COMMAND ${CMAKE_BINARY_DIR}/bin/gen --input ${widget_file} --widgetid ${widget_name} > ${output_fn}.tmp
    COMMAND ${CMAKE_COMMAND} -E copy_if_different ${output_fn}.tmp ${output_fn}
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
    MAIN_DEPENDENCY ${widget_file}
    DEPENDS gen)

  list(APPEND generated_files ${output_fn})
endforeach()

# Generate preference types from data/pref.xml
set(pref_xml ${CMAKE_SOURCE_DIR}/data/pref.xml)

set(output_fn ${CMAKE_CURRENT_BINARY_DIR}/pref.xml.h)
add_custom_command(
  OUTPUT ${output_fn}
  COMMAND ${CMAKE_BINARY_DIR}/bin/gen --input ${pref_xml} --pref-h > ${output_fn}.tmp
  COMMAND ${CMAKE_COMMAND} -E copy_if_different ${output_fn}.tmp ${output_fn}
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
  MAIN_DEPENDENCY ${pref_xml}
  DEPENDS gen)
list(APPEND generated_files ${output_fn})

set(output_fn ${CMAKE_CURRENT_BINARY_DIR}/pref.xml.cpp)
add_custom_command(
  OUTPUT ${output_fn}
  COMMAND ${CMAKE_BINARY_DIR}/bin/gen --input ${pref_xml} --pref-cpp > ${output_fn}.tmp
  COMMAND ${CMAKE_COMMAND} -E copy_if_different ${output_fn}.tmp ${output_fn}
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
  MAIN_DEPENDENCY ${pref_xml}
  DEPENDS gen)
list(APPEND generated_files ${output_fn})

# Generate generated_skin.h from data/skins/default/skin.xml
set(skin_xml ${CMAKE_SOURCE_DIR}/data/skins/default/skin.xml)
set(output_fn ${CMAKE_CURRENT_BINARY_DIR}/skin.xml.h)
add_custom_command(
  OUTPUT ${output_fn}
  COMMAND ${CMAKE_BINARY_DIR}/bin/gen --input ${skin_xml} --skin > ${output_fn}.tmp
  COMMAND ${CMAKE_COMMAND} -E copy_if_different ${output_fn}.tmp ${output_fn}
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
  MAIN_DEPENDENCY ${skin_xml}
  DEPENDS gen)
list(APPEND generated_files ${output_fn})

# Directory where generated files by "gen" utility will stay.
include_directories(${CMAKE_CURRENT_BINARY_DIR})

if(GEN_ONLY)
  add_library(app-lib ${generated_files})
else()

if(WITH_WEBP_SUPPORT)
  add_definitions(-DASEPRITE_WITH_WEBP_SUPPORT)
endif()

######################################################################
# app-lib target

set(data_recovery_files
  crash/backup_observer.cpp
  crash/data_recovery.cpp
  crash/read_document.cpp
  crash/session.cpp
  crash/write_document.cpp
  ui/data_recovery_view.cpp)

set(file_formats
  file/ase_format.cpp
  file/bmp_format.cpp
  file/extension_format.cpp
  file/fli_format.cpp
  file/gif_format.cpp
  file/ico_format.cpp
  file/jpeg_format.cpp
  file/pcx_format.cpp
  file/pixly_format.cpp
  file/png_format.cpp
  file/qoi_format.cpp
  file/she_format.cpp
  file/tga_format.cpp
  file/webp_format.cpp
)

add_library(app-lib
  app.cpp
  app_brushes.cpp
  app_menus.cpp
  app_options.cpp
  app_render.cpp
  cmd.cpp
  cmd/add_cel.cpp
  cmd/add_frame.cpp
  cmd/add_frame_tag.cpp
  cmd/add_layer.cpp
  cmd/add_palette.cpp
  cmd/background_from_layer.cpp
  cmd/clear_cel.cpp
  cmd/clear_image.cpp
  cmd/clear_mask.cpp
  cmd/clear_rect.cpp
  cmd/configure_background.cpp
  cmd/copy_cel.cpp
  cmd/copy_frame.cpp
  cmd/copy_rect.cpp
  cmd/copy_region.cpp
  cmd/crop_cel.cpp
  cmd/deselect_mask.cpp
  cmd/flatten_layers.cpp
  cmd/flip_image.cpp
  cmd/flip_mask.cpp
  cmd/flip_masked_cel.cpp
  cmd/layer_from_background.cpp
  cmd/move_cel.cpp
  cmd/move_layer.cpp
  cmd/patch_cel.cpp
  cmd/remap_colors.cpp
  cmd/remove_cel.cpp
  cmd/remove_frame.cpp
  cmd/remove_frame_tag.cpp
  cmd/remove_layer.cpp
  cmd/remove_palette.cpp
  cmd/replace_image.cpp
  cmd/reselect_mask.cpp
  cmd/set_cel_data.cpp
  cmd/set_cel_frame.cpp
  cmd/set_cel_opacity.cpp
  cmd/set_cel_position.cpp
  cmd/set_frame_duration.cpp
  cmd/set_frame_tag_anidir.cpp
  cmd/set_frame_tag_color.cpp
  cmd/set_frame_tag_name.cpp
  cmd/set_frame_tag_range.cpp
  cmd/set_layer_blend_mode.cpp
  cmd/set_layer_flags.cpp
  cmd/set_layer_name.cpp
  cmd/set_layer_opacity.cpp
  cmd/set_mask.cpp
  cmd/set_mask_position.cpp
  cmd/set_palette.cpp
  cmd/set_pixel_format.cpp
  cmd/set_sprite_size.cpp
  cmd/set_total_frames.cpp
  cmd/set_transparent_color.cpp
  cmd/set_user_data.cpp
  cmd/shift_masked_cel.cpp
  cmd/trim_cel.cpp
  cmd/unlink_cel.cpp
  cmd/with_cel.cpp
  cmd/with_document.cpp
  cmd/with_frame_tag.cpp
  cmd/with_image.cpp
  cmd/with_layer.cpp
  cmd/with_sprite.cpp
  cmd_sequence.cpp
  cmd_transaction.cpp
  color.cpp
  color_picker.cpp
  color_utils.cpp
  commands/cmd_about.cpp
  commands/cmd_add_color.cpp
  commands/cmd_advanced_mode.cpp
  commands/cmd_alternate_timeline.cpp
  commands/cmd_alternate_toolbar.cpp
  commands/cmd_alternate_touchbar.cpp
  commands/cmd_background_from_layer.cpp
  commands/cmd_cancel.cpp
  commands/cmd_canvas_size.cpp
  commands/cmd_cel_properties.cpp
  commands/cmd_change_brush.cpp
  commands/cmd_change_color.cpp
  commands/cmd_change_pixel_format.cpp
  commands/cmd_clear.cpp
  commands/cmd_clear_cel.cpp
  commands/cmd_close_file.cpp
  commands/cmd_color_quantization.cpp
  commands/cmd_copy.cpp
  commands/cmd_copy_cel.cpp
  commands/cmd_copy_merged.cpp
  commands/cmd_crop.cpp
  commands/cmd_cut.cpp
  commands/cmd_deselect_mask.cpp
  commands/cmd_developer_console.cpp
  commands/cmd_discard_brush.cpp
  commands/cmd_duplicate_layer.cpp
  commands/cmd_duplicate_sprite.cpp
  commands/cmd_duplicate_view.cpp
  commands/cmd_exit.cpp
  commands/cmd_export_sprite_sheet.cpp
  commands/cmd_eyedropper.cpp
  commands/cmd_flatten_layers.cpp
  commands/cmd_flip.cpp
  commands/cmd_frame_properties.cpp
  commands/cmd_frame_tag_properties.cpp
  commands/cmd_fullscreen_preview.cpp
  commands/cmd_goto_frame.cpp
  commands/cmd_goto_layer.cpp
  commands/cmd_goto_tab.cpp
  commands/cmd_grid.cpp
  commands/cmd_home.cpp
  commands/cmd_import_sprite_sheet.cpp
  commands/cmd_install_script.cpp
  commands/cmd_invert_mask.cpp
  commands/cmd_keyboard_shortcuts.cpp
  commands/cmd_launch.cpp
  commands/cmd_layer_from_background.cpp
  commands/cmd_layer_properties.cpp
  commands/cmd_layer_visibility.cpp
  commands/cmd_link_cels.cpp
  commands/cmd_load_mask.cpp
  commands/cmd_load_palette.cpp
  commands/cmd_mask_all.cpp
  commands/cmd_mask_by_color.cpp
  commands/cmd_mask_content.cpp
  commands/cmd_merge_down_layer.cpp
  commands/cmd_modify_selection.cpp
  commands/cmd_move_cel.cpp
  commands/cmd_move_mask.cpp
  commands/cmd_new_brush.cpp
  commands/cmd_new_file.cpp
  commands/cmd_new_frame.cpp
  commands/cmd_new_frame_tag.cpp
  commands/cmd_new_layer.cpp
  commands/cmd_new_layer_set.cpp
  commands/cmd_new_sprite_from_selection.cpp
  commands/cmd_onionskin.cpp
  commands/cmd_open_file.cpp
  commands/cmd_open_in_folder.cpp
  commands/cmd_open_scripts_folder.cpp
  commands/cmd_open_with_app.cpp
  commands/cmd_options.cpp
  commands/cmd_palette_editor.cpp
  commands/cmd_palette_size.cpp
  commands/cmd_paste.cpp
  commands/cmd_paste_text.cpp
  commands/cmd_pixel_perfect_mode.cpp
  commands/cmd_play_animation.cpp
  commands/cmd_refresh.cpp
  commands/cmd_remove_frame.cpp
  commands/cmd_remove_frame_tag.cpp
  commands/cmd_remove_layer.cpp
  commands/cmd_repeat_last_export.cpp
  commands/cmd_reselect_mask.cpp
  commands/cmd_reverse_frames.cpp
  commands/cmd_rotate.cpp
  commands/cmd_run_script.cpp
  commands/cmd_rescan_scripts.cpp
  commands/cmd_save_file.cpp
  commands/cmd_save_mask.cpp
  commands/cmd_save_palette.cpp
  commands/cmd_scroll.cpp
  commands/cmd_scroll_center.cpp
  commands/cmd_select_tile.cpp
  commands/cmd_set_color_selector.cpp
  commands/cmd_set_ink_type.cpp
  commands/cmd_set_loop_section.cpp
  commands/cmd_set_palette.cpp
  commands/cmd_set_palette_entry_size.cpp
  commands/cmd_set_same_ink.cpp
  commands/cmd_share.cpp
  commands/cmd_show.cpp
  commands/cmd_sprite_properties.cpp
  commands/cmd_sprite_size.cpp
  commands/cmd_switch_colors.cpp
  commands/cmd_symmetry_mode.cpp
  commands/cmd_tiled_mode.cpp
  commands/cmd_timeline.cpp
  commands/cmd_toggle_fullscreen.cpp
  commands/cmd_toggle_preview.cpp
  commands/cmd_toggle_touchbar.cpp
  commands/cmd_undo.cpp
  commands/cmd_undo_history.cpp
  commands/cmd_unlink_cel.cpp
  commands/cmd_zoom.cpp
  commands/command.cpp
  commands/commands.cpp
  commands/filters/cmd_color_curve.cpp
  commands/filters/cmd_convolution_matrix.cpp
  commands/filters/cmd_despeckle.cpp
  commands/filters/cmd_invert_color.cpp
  commands/filters/cmd_replace_color.cpp
  commands/filters/color_curve_editor.cpp
  commands/filters/convolution_matrix_stock.cpp
  commands/filters/filter_manager_impl.cpp
  commands/filters/filter_preview.cpp
  commands/filters/filter_target_buttons.cpp
  commands/filters/filter_window.cpp
  commands/filters/filter_worker.cpp
  console.cpp
  context.cpp
  context_flags.cpp
  document.cpp
  document_api.cpp
  document_exporter.cpp
  document_range.cpp
  document_range_ops.cpp
  document_undo.cpp
  extra_cel.cpp
  file/file.cpp
  file/file_format.cpp
  file/file_formats_manager.cpp
  file/palette_file.cpp
  file/split_filename.cpp
  ${file_formats}
  file_selector.cpp
  file_system.cpp
  filename_formatter.cpp
  flatten.cpp
  gui_xml.cpp
  ini_file.cpp
  job.cpp
  launcher.cpp
  log.cpp
  loop_tag.cpp
  modules.cpp
  modules/editors.cpp
  modules/gfx.cpp
  modules/i18n.cpp
  modules/gui.cpp
  modules/palettes.cpp
  pref/preferences.cpp
  project.cpp
  recent_files.cpp
  recent_files_menu.cpp
  res/palettes_loader_delegate.cpp
  res/resources_loader.cpp
  resource_finder.cpp

  script/app_scripting.cpp
  script/app_scripting.h
  script/console_delegate.cpp
  script/script_menu.cpp
  script/script_menu.h

  script/api/app_script.cpp
  script/api/button_script.cpp
  script/api/cel_script.cpp
  script/api/colormode_script.cpp
  script/api/command_script.cpp
  script/api/console_script.cpp
  script/api/dialog_script.cpp
  script/api/document_script.cpp
  script/api/image_script.cpp
  script/api/imageview_script.cpp
  script/api/intentry_script.cpp
  script/api/entry_script.cpp
  script/api/layer_script.cpp
  script/api/label_script.cpp
  script/api/pixelcolor_script.cpp
  script/api/palette_script.cpp
  script/api/palettelistbox_script.cpp
  script/api/storage_script.cpp
  script/api/sprite_script.cpp
  script/api/selection_script.cpp

  send_crash.cpp
  shade.cpp
  shell.cpp
  snap_to_grid.cpp
  thumbnail_generator.cpp
  tools/active_tool.cpp
  tools/ink_type.cpp
  tools/intertwine.cpp
  tools/pick_ink.cpp
  tools/point_shape.cpp
  tools/stroke.cpp
  tools/symmetry.cpp
  tools/tool_box.cpp
  tools/tool_loop_manager.cpp
  transaction.cpp
  transformation.cpp
  ui/ani_controls.cpp
  ui/app_menuitem.cpp
  ui/brush_popup.cpp
  ui/button_set.cpp
  ui/color_bar.cpp
  ui/touch_bar.cpp
  ui/color_button.cpp
  ui/color_popup.cpp
  ui/color_selector.cpp
  ui/color_sliders.cpp
  ui/color_spectrum.cpp
  ui/color_tint_shade_tone.cpp
  ui/color_wheel.cpp
  ui/configure_timeline_popup.cpp
  ui/context_bar.cpp
  ui/devconsole_view.cpp
  ui/document_view.cpp
  ui/drop_down_button.cpp
  ui/editor/brush_preview.cpp
  ui/editor/drawing_state.cpp
  ui/editor/editor.cpp
  ui/editor/editor_observers.cpp
  ui/editor/editor_states_history.cpp
  ui/editor/editor_view.cpp
  ui/editor/moving_cel_state.cpp
  ui/editor/moving_pixels_state.cpp
  ui/editor/moving_symmetry_state.cpp
  ui/editor/navigate_state.cpp
  ui/editor/pivot_helpers.cpp
  ui/editor/pixels_movement.cpp
  ui/editor/play_state.cpp
  ui/editor/scrolling_state.cpp
  ui/editor/select_box_state.cpp
  ui/editor/standby_state.cpp
  ui/editor/state_with_wheel_behavior.cpp
  ui/editor/tool_loop_impl.cpp
  ui/editor/transform_handles.cpp
  ui/editor/zooming_state.cpp
  ui/file_list.cpp
  ui/file_list_view.cpp
  ui/file_selector.cpp
  ui/font_popup.cpp
  ui/frame_tag_window.cpp
  ui/hex_color_entry.cpp
  ui/home_view.cpp
  ui/icon_button.cpp
  ui/input_chain.cpp
  ui/keyboard_shortcuts.cpp
  ui/main_menu_bar.cpp
  ui/main_window.cpp
  ui/notifications.cpp
  ui/palette_popup.cpp
  ui/palette_view.cpp
  ui/palette_listbox.cpp
  ui/popup_window_pin.cpp
  ui/preview_editor.cpp
  ui/recent_listbox.cpp
  ui/search_entry.cpp
  ui/select_accelerator.cpp
  ui/skin/button_icon_impl.cpp
  ui/skin/skin_part.cpp
  ui/skin/skin_property.cpp
  ui/skin/skin_slider_property.cpp
  ui/skin/skin_style_property.cpp
  ui/skin/skin_theme.cpp
  ui/skin/style.cpp
  ui/skin/style_sheet.cpp
  ui/status_bar.cpp
  ui/styled_button.cpp
  ui/tabs.cpp
  ui/timeline.cpp
  ui/toolbar.cpp
  ui/user_data_popup.cpp
  ui/workspace.cpp
  ui/workspace_panel.cpp
  ui/workspace_tabs.cpp
  ui/zoom_entry.cpp
  ui_context.cpp
  util/autocrop.cpp
  util/clipboard.cpp
  util/clipboard_native.cpp
  util/create_cel_copy.cpp
  util/expand_cel_canvas.cpp
  util/filetoks.cpp
  util/freetype_utils.cpp
  util/msk_file.cpp
  util/new_image_from_mask.cpp
  util/pic_file.cpp
  util/range_utils.cpp
  widget_loader.cpp
  xml_document.cpp
  ${data_recovery_files}
  ${generated_files})
endif()
