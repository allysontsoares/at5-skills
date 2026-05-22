# 📦 Advanced & Complex Components

Componentes complexos de alta densidade de recursos, como filtros avançados, quadros Kanban estruturados, mapas interativos e menus de comando.

---

## 📂 `filters.tsx`

Caminho original no repositório: `/packages/ui/src/components/filters.tsx`

```tsx
'use client';

import { useRender } from '@base-ui/react/use-render';
import { cva } from 'class-variance-authority';
import type React from 'react';
import {
  createContext,
  useCallback,
  useContext,
  useEffect,
  useId,
  useMemo,
  useRef,
  useState,
} from 'react';

import { AlertCircleIcon, CheckIcon, XIcon } from 'lucide-react';
import { cn } from '..';
import { Button } from './button';
import { ButtonGroup, ButtonGroupText } from './button-group';
import {
  DropdownMenu,
  DropdownMenuCheckboxItem,
  DropdownMenuContent,
  DropdownMenuGroup,
  DropdownMenuItem,
  DropdownMenuSeparator,
  DropdownMenuSub,
  DropdownMenuSubContent,
  DropdownMenuSubTrigger,
  DropdownMenuTrigger,
} from './dropdown-menu';
import { Input } from './input';
import {
  InputGroup,
  InputGroupAddon,
  InputGroupButton,
  InputGroupInput,
  InputGroupText,
} from './input-group';
import { Kbd } from './kbd';
import { ScrollArea } from './scroll-area';
import { Tooltip, TooltipContent, TooltipTrigger } from './tooltip';

// i18n Configuration Interface
export interface FilterI18nConfig {
  // UI Labels
  addFilter: string;
  searchFields: string;
  noFieldsFound: string;
  noResultsFound: string;
  select: string;
  true: string;
  false: string;
  min: string;
  max: string;
  to: string;
  typeAndPressEnter: string;
  selected: string;
  selectedCount: string;
  percent: string;
  defaultCurrency: string;
  defaultColor: string;
  addFilterTitle: string;

  // Operators
  operators: {
    is: string;
    isNot: string;
    isAnyOf: string;
    isNotAnyOf: string;
    includesAll: string;
    excludesAll: string;
    before: string;
    after: string;
    between: string;
    notBetween: string;
    contains: string;
    notContains: string;
    startsWith: string;
    endsWith: string;
    isExactly: string;
    equals: string;
    notEquals: string;
    greaterThan: string;
    lessThan: string;
    overlaps: string;
    includes: string;
    excludes: string;
    includesAllOf: string;
    includesAnyOf: string;
    empty: string;
    notEmpty: string;
  };

  // Placeholders
  placeholders: {
    enterField: (fieldType: string) => string;
    selectField: string;
    searchField: (fieldName: string) => string;
    enterKey: string;
    enterValue: string;
  };

  // Helper functions
  helpers: {
    formatOperator: (operator: string) => string;
  };

  // Validation
  validation: {
    invalidEmail: string;
    invalidUrl: string;
    invalidTel: string;
    invalid: string;
  };
}

// Default English i18n configuration
export const DEFAULT_I18N: FilterI18nConfig = {
  // UI Labels
  addFilter: 'Filter',
  searchFields: 'Filter...',
  noFieldsFound: 'No filters found.',
  noResultsFound: 'No results found.',
  select: 'Select...',
  true: 'True',
  false: 'False',
  min: 'Min',
  max: 'Max',
  to: 'to',
  typeAndPressEnter: 'Type and press Enter to add tag',
  selected: 'selected',
  selectedCount: 'selected',
  percent: '%',
  defaultCurrency: '$',
  defaultColor: '#000000',
  addFilterTitle: 'Add filter',

  // Operators
  operators: {
    is: 'is',
    isNot: 'is not',
    isAnyOf: 'is any of',
    isNotAnyOf: 'is not any of',
    includesAll: 'includes all',
    excludesAll: 'excludes all',
    before: 'before',
    after: 'after',
    between: 'between',
    notBetween: 'not between',
    contains: 'contains',
    notContains: 'does not contain',
    startsWith: 'starts with',
    endsWith: 'ends with',
    isExactly: 'is exactly',
    equals: 'equals',
    notEquals: 'not equals',
    greaterThan: 'greater than',
    lessThan: 'less than',
    overlaps: 'overlaps',
    includes: 'includes',
    excludes: 'excludes',
    includesAllOf: 'includes all of',
    includesAnyOf: 'includes any of',
    empty: 'is empty',
    notEmpty: 'is not empty',
  },

  // Placeholders
  placeholders: {
    enterField: (fieldType: string) => `Enter ${fieldType}...`,
    selectField: 'Select...',
    searchField: (fieldName: string) => `Search ${fieldName.toLowerCase()}...`,
    enterKey: 'Enter key...',
    enterValue: 'Enter value...',
  },

  // Helper functions
  helpers: {
    formatOperator: (operator: string) => operator.replace(/_/g, ' '),
  },

  // Validation
  validation: {
    invalidEmail: 'Invalid email format',
    invalidUrl: 'Invalid URL format',
    invalidTel: 'Invalid phone format',
    invalid: 'Invalid input format',
  },
};

// Context for all Filter component props
interface FilterContextValue {
  variant: 'solid' | 'default';
  size: 'sm' | 'default' | 'lg';
  radius: 'default' | 'full';
  i18n: FilterI18nConfig;
  className?: string;
  showSearchInput?: boolean;
  trigger?: React.ReactNode;
  allowMultiple?: boolean;
}

const FilterContext = createContext<FilterContextValue>({
  variant: 'default',
  size: 'default',
  radius: 'default',
  i18n: DEFAULT_I18N,
  className: undefined,
  showSearchInput: true,
  trigger: undefined,
  allowMultiple: true,
});

const useFilterContext = () => useContext(FilterContext);

// Container variant for filters wrapper
const filtersContainerVariants = cva('flex flex-wrap items-center', {
  variants: {
    variant: {
      solid: 'gap-2',
      default: '',
    },
    size: {
      sm: 'gap-1',
      default: 'gap-2',
      lg: 'gap-3',
    },
  },
  defaultVariants: {
    variant: 'default',
    size: 'sm',
  },
});

function FilterInput<T = unknown>({
  field,
  onBlur,
  onKeyDown,
  className,
  ...props
}: React.InputHTMLAttributes<HTMLInputElement> & {
  className?: string;
  field?: FilterFieldConfig<T>;
}) {
  const context = useFilterContext();
  const [isValid, setIsValid] = useState(true);
  const [validationMessage, setValidationMessage] = useState('');
  const inputRef = useRef<HTMLInputElement>(null);

  useEffect(() => {
    if (!props.autoFocus) return;
    const timer = setTimeout(() => {
      inputRef.current?.focus();
    }, 300);
    return () => clearTimeout(timer);
  }, [props.autoFocus]);

  // Validation function to check if input matches pattern
  const validateInput = (value: string, pattern?: string): boolean => {
    if (!pattern || !value) return true;
    const regex = new RegExp(pattern);
    return regex.test(value);
  };

  // Get validation message for field type
  const getValidationMessage = (): string => {
    return context.i18n.validation.invalid;
  };

  // Handle blur event - validate when user leaves input
  const handleBlur = (e: React.FocusEvent<HTMLInputElement>) => {
    const value = e.target.value;
    const pattern = field?.pattern || props.pattern;

    // Only validate if there's a value and (pattern or validation function)
    if (value && (pattern || field?.validation)) {
      let valid = true;
      let customMessage = '';

      // If there's a custom validation function, use it
      if (field?.validation) {
        const result = field.validation(value);
        // Handle both boolean and object return types
        if (typeof result === 'boolean') {
          valid = result;
        } else {
          valid = result.valid;
          customMessage = result.message || '';
        }
      } else if (pattern) {
        // Use pattern validation
        valid = validateInput(value, pattern);
      }

      setIsValid(valid);
      setValidationMessage(valid ? '' : customMessage || getValidationMessage());
    } else {
      // Reset validation state for empty values or no validation
      setIsValid(true);
      setValidationMessage('');
    }

    // Call the original onBlur if provided
    onBlur?.(e);
  };

  // Handle keydown event - hide validation error when user starts typing
  const handleKeyDown = (e: React.KeyboardEvent<HTMLInputElement>) => {
    // Hide validation error when user starts typing (any key except special keys)
    if (
      !isValid &&
      !['Tab', 'Escape', 'Enter', 'ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight'].includes(e.key)
    ) {
      setIsValid(true);
      setValidationMessage('');
    }

    // Call the original onKeyDown if provided
    onKeyDown?.(e);
  };

  return (
    <InputGroup
      className={cn(
        'w-36',
        context.size === 'sm' && 'h-7!',
        context.size === 'default' && 'h-8!',
        context.size === 'lg' && 'h-9!',
        className,
      )}
    >
      {field?.prefix && (
        <InputGroupAddon>
          <InputGroupText>{field.prefix}</InputGroupText>
        </InputGroupAddon>
      )}
      <InputGroupInput
        ref={inputRef}
        aria-invalid={!isValid}
        aria-describedby={
          !isValid && validationMessage ? `${field?.key || 'input'}-error` : undefined
        }
        onBlur={handleBlur}
        onKeyDown={handleKeyDown}
        className={cn(
          context.size === 'sm' && 'h-7! text-xs',
          context.size === 'default' && 'h-8!',
          context.size === 'lg' && 'h-9!',
        )}
        {...props}
      />
      {!isValid && validationMessage && (
        <InputGroupAddon align="inline-end">
          <Tooltip>
            <TooltipTrigger render={<InputGroupButton size="icon-xs" />}>
              <AlertCircleIcon className="text-red-11 size-3.5" />
            </TooltipTrigger>
            <TooltipContent>
              <p className="text-sm">{validationMessage}</p>
            </TooltipContent>
          </Tooltip>
        </InputGroupAddon>
      )}

      {field?.suffix && (
        <InputGroupAddon align="inline-end">
          <InputGroupText>{field.suffix}</InputGroupText>
        </InputGroupAddon>
      )}
    </InputGroup>
  );
}

interface FilterRemoveButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  icon?: React.ReactNode;
}

function FilterRemoveButton({ className, icon = <XIcon />, ...props }: FilterRemoveButtonProps) {
  const context = useFilterContext();

  return (
    <Button
      variant="outline"
      size={context.size === 'sm' ? 'icon-sm' : 'icon'}
      className={cn(
        'border-mauve-6 dark:border-mauve-6 text-mauve-9 shadow-none hover:bg-mauve-2 dark:hover:bg-mauve-2 hover:text-mauve-12',
        context.size === 'sm' && 'size-7 min-h-0',
        className,
      )}
      {...props}
    >
      {icon}
    </Button>
  );
}

// Generic types for flexible filter system
export interface FilterOption<T = unknown> {
  value: T;
  label: string;
  icon?: React.ReactNode;
  metadata?: Record<string, unknown>;
  className?: string;
}

export interface FilterOperator {
  value: string;
  label: string;
  supportsMultiple?: boolean;
}

// Custom renderer props interface
export interface CustomRendererProps<T = unknown> {
  field: FilterFieldConfig<T>;
  values: T[];
  onChange: (values: T[]) => void;
  operator: string;
}

// Grouped field configuration interface
export interface FilterFieldGroup<T = unknown> {
  group?: string;
  fields: FilterFieldConfig<T>[];
}

// Union type for both flat and grouped field configurations
export type FilterFieldsConfig<T = unknown> = FilterFieldConfig<T>[] | FilterFieldGroup<T>[];

export interface FilterFieldConfig<T = unknown> {
  key?: string;
  label?: string;
  icon?: React.ReactNode;
  type?: 'select' | 'multiselect' | 'text' | 'custom' | 'separator';
  // Group-level configuration
  group?: string;
  fields?: FilterFieldConfig<T>[];
  // Field-specific options
  options?: FilterOption<T>[];
  operators?: FilterOperator[];
  customRenderer?: (props: CustomRendererProps<T>) => React.ReactNode;
  customValueRenderer?: (values: T[], options: FilterOption<T>[]) => React.ReactNode;
  placeholder?: string;
  searchable?: boolean;
  maxSelections?: number;
  min?: number;
  max?: number;
  step?: number;
  prefix?: string | React.ReactNode;
  suffix?: string | React.ReactNode;
  pattern?: string;
  validation?: (value: unknown) => boolean | { valid: boolean; message?: string };
  allowCustomValues?: boolean;
  className?: string;
  menuPopupClassName?: string;
  // Grouping options (legacy support)
  groupLabel?: string;
  // Boolean field options
  onLabel?: string;
  offLabel?: string;
  // Input event handlers
  onInputChange?: (e: React.ChangeEvent<HTMLInputElement>) => void;
  // Default operator to use when creating a filter for this field
  defaultOperator?: string;
  // Controlled values support for this field
  value?: T[];
  onValueChange?: (values: T[]) => void;
}

// Helper functions to handle both flat and grouped field configurations
const isFieldGroup = <T = unknown>(
  item: FilterFieldConfig<T> | FilterFieldGroup<T>,
): item is FilterFieldGroup<T> => {
  return 'fields' in item && Array.isArray(item.fields);
};

// Helper function to check if a FilterFieldConfig is a group-level configuration
const isGroupLevelField = <T = unknown>(field: FilterFieldConfig<T>): boolean => {
  return Boolean(field.group && field.fields);
};

const flattenFields = <T = unknown>(fields: FilterFieldsConfig<T>): FilterFieldConfig<T>[] => {
  return fields.reduce<FilterFieldConfig<T>[]>((acc, item) => {
    if (isFieldGroup(item)) {
      // biome-ignore lint/performance/noAccumulatingSpread: <explanation>
      return [...acc, ...item.fields];
    }
    // Handle group-level fields (new structure)
    if (isGroupLevelField(item)) {
      // biome-ignore lint/performance/noAccumulatingSpread: <explanation>
      return [...acc, ...(item.fields ?? [])];
    }
    // biome-ignore lint/performance/noAccumulatingSpread: <explanation>
    return [...acc, item];
  }, []);
};

const getFieldsMap = <T = unknown>(
  fields: FilterFieldsConfig<T>,
): Record<string, FilterFieldConfig<T>> => {
  const flatFields = flattenFields(fields);
  return flatFields.reduce(
    (acc, field) => {
      // Only add fields that have a key (skip group-level configurations)
      if (field.key) {
        acc[field.key] = field;
      }
      return acc;
    },
    {} as Record<string, FilterFieldConfig<T>>,
  );
};

// Helper function to create operators from i18n config
const createOperatorsFromI18n = (i18n: FilterI18nConfig): Record<string, FilterOperator[]> => ({
  select: [
    { value: 'is', label: i18n.operators.is },
    { value: 'is_not', label: i18n.operators.isNot },
    { value: 'empty', label: i18n.operators.empty },
    { value: 'not_empty', label: i18n.operators.notEmpty },
  ],
  multiselect: [
    { value: 'is_any_of', label: i18n.operators.isAnyOf },
    { value: 'is_not_any_of', label: i18n.operators.isNotAnyOf },
    { value: 'includes_all', label: i18n.operators.includesAll },
    { value: 'excludes_all', label: i18n.operators.excludesAll },
    { value: 'empty', label: i18n.operators.empty },
    { value: 'not_empty', label: i18n.operators.notEmpty },
  ],
  text: [
    { value: 'contains', label: i18n.operators.contains },
    { value: 'not_contains', label: i18n.operators.notContains },
    { value: 'starts_with', label: i18n.operators.startsWith },
    { value: 'ends_with', label: i18n.operators.endsWith },
    { value: 'is', label: i18n.operators.isExactly },
    { value: 'empty', label: i18n.operators.empty },
    { value: 'not_empty', label: i18n.operators.notEmpty },
  ],
  custom: [
    { value: 'is', label: i18n.operators.is },
    { value: 'after', label: i18n.operators.after },
    { value: 'is', label: i18n.operators.is },
    { value: 'between', label: i18n.operators.between },
    { value: 'empty', label: i18n.operators.empty },
    { value: 'not_empty', label: i18n.operators.notEmpty },
  ],
});

// Default operators for different field types (using default i18n)
export const DEFAULT_OPERATORS: Record<string, FilterOperator[]> =
  createOperatorsFromI18n(DEFAULT_I18N);

// Helper function to get operators for a field
const getOperatorsForField = <T = unknown>(
  field: FilterFieldConfig<T>,
  values: T[],
  i18n: FilterI18nConfig,
): FilterOperator[] => {
  if (field.operators) return field.operators;

  const operators = createOperatorsFromI18n(i18n);

  // Determine field type for operator selection
  let fieldType = field.type || 'select';

  // If it's a select field but has multiple values, treat as multiselect
  if (fieldType === 'select' && values.length > 1) {
    fieldType = 'multiselect';
  }

  // If it's a multiselect field or has multiselect operators, use multiselect operators
  if (fieldType === 'multiselect' || field.type === 'multiselect') {
    return operators.multiselect;
  }

  return operators[fieldType] || operators.select;
};

interface FilterOperatorDropdownProps<T = unknown> {
  field: FilterFieldConfig<T>;
  operator: string;
  values: T[];
  onChange: (operator: string) => void;
}

function FilterOperatorDropdown<T = unknown>({
  field,
  operator,
  values,
  onChange,
}: FilterOperatorDropdownProps<T>) {
  const context = useFilterContext();
  const operators = getOperatorsForField(field, values, context.i18n);

  // Find the operator label, with fallback to formatted operator name
  const operatorLabel =
    operators.find((op) => op.value === operator)?.label ||
    context.i18n.helpers.formatOperator(operator);

  // Single operator — render as static, non-interactive text
  if (operators.length <= 1) {
    return (
      <ButtonGroupText
        className={cn(
          'bg-mauve-2 dark:bg-mauve-2 border-mauve-6 dark:border-mauve-6 text-mauve-9 shadow-none',
          context.size === 'sm' && 'text-xs px-2 h-7 min-h-0',
        )}
      >
        {operatorLabel}
      </ButtonGroupText>
    );
  }

  return (
    <DropdownMenu>
      <DropdownMenuTrigger
        render={
          <Button
            variant="outline"
            size={context.size}
            className={cn(
              'border-mauve-6 dark:border-mauve-6 text-mauve-9 shadow-none hover:bg-mauve-2 dark:hover:bg-mauve-2 hover:text-mauve-12',
              context.size === 'sm' && 'text-xs px-2 h-7 min-h-0',
            )}
          >
            {operatorLabel}
          </Button>
        }
      />
      <DropdownMenuContent align="start" className="w-fit min-w-fit">
        {operators.map((op) => (
          <DropdownMenuItem
            key={op.value}
            onClick={() => onChange(op.value)}
            className={cn(
              'data-highlighted:bg-mauve-4 data-highlighted:text-mauve-12 flex items-center justify-between',
            )}
          >
            <span>{op.label}</span>
            <CheckIcon
              className={cn(
                'text-brand-9 ms-auto',
                op.value === operator ? 'opacity-100' : 'opacity-0',
              )}
            />
          </DropdownMenuItem>
        ))}
      </DropdownMenuContent>
    </DropdownMenu>
  );
}

interface FilterValueSelectorProps<T = unknown> {
  field: FilterFieldConfig<T>;
  values: T[];
  onChange: (values: T[]) => void;
  operator: string;
  autoFocus?: boolean;
}

interface SelectOptionsPopoverProps<T = unknown> {
  field: FilterFieldConfig<T>;
  values: T[];
  onChange: (values: T[]) => void;
  onClose?: () => void;
  inline?: boolean;
}

function SelectOptionsPopover<T = unknown>({
  field,
  values,
  onChange,
  onClose,
  inline = false,
}: SelectOptionsPopoverProps<T>) {
  const [open, setOpen] = useState(false);
  const [searchInput, setSearchInput] = useState('');
  const [highlightedIndex, setHighlightedIndex] = useState(-1);
  const inputRef = useRef<HTMLInputElement>(null);
  const context = useFilterContext();
  const baseId = useId();

  useEffect(() => {
    if (open) {
      inputRef.current?.focus();
    }
  }, [open]);

  // biome-ignore lint/correctness/useExhaustiveDependencies: searchInput e open são triggers intencionais
  useEffect(() => {
    setHighlightedIndex(-1);
  }, [searchInput, open]);

  useEffect(() => {
    if (highlightedIndex >= 0 && open) {
      const element = document.getElementById(`${baseId}-item-${highlightedIndex}`);
      element?.scrollIntoView({ block: 'nearest' });
    }
  }, [highlightedIndex, open, baseId]);

  const isMultiSelect = field.type === 'multiselect' || values.length > 1;
  const effectiveValues = (field.value !== undefined ? (field.value as T[]) : values) || [];

  const selectedOptions = field.options?.filter((opt) => effectiveValues.includes(opt.value)) || [];
  const unselectedOptions =
    field.options?.filter((opt) => !effectiveValues.includes(opt.value)) || [];

  // Filter options based on search input
  const filteredSelectedOptions = selectedOptions; // Keep all selected visible
  const filteredUnselectedOptions = unselectedOptions.filter((opt) =>
    opt.label.toLowerCase().includes(searchInput.toLowerCase()),
  );

  const allFilteredOptions = useMemo(
    () => [...filteredSelectedOptions, ...filteredUnselectedOptions],
    [filteredSelectedOptions, filteredUnselectedOptions],
  );

  const handleClose = () => {
    setOpen(false);
    onClose?.();
  };

  const renderMenuContent = () => (
    <>
      {field.searchable !== false && (
        <>
          <Input
            ref={inputRef}
            role="combobox"
            aria-autocomplete="list"
            aria-expanded={true}
            aria-haspopup="listbox"
            aria-controls={`${baseId}-listbox`}
            aria-activedescendant={
              highlightedIndex >= 0 ? `${baseId}-item-${highlightedIndex}` : undefined
            }
            placeholder={context.i18n.placeholders.searchField(field.label || '')}
            className={cn(
              'border-mauve-6 h-8 rounded-none border-0 bg-transparent! px-2 text-sm shadow-none',
              'focus-visible:border-mauve-7 focus-visible:ring-0 focus-visible:ring-offset-0',
              open && 'placeholder:text-mauve-12',
            )}
            value={searchInput}
            onChange={(e) => setSearchInput(e.target.value)}
            onBlur={() => open && inputRef.current?.focus()}
            onClick={(e) => e.stopPropagation()}
            onKeyDown={(e) => {
              if (e.key === 'ArrowDown') {
                e.preventDefault();
                if (allFilteredOptions.length > 0) {
                  setHighlightedIndex((prev) =>
                    prev < allFilteredOptions.length - 1 ? prev + 1 : 0,
                  );
                }
              } else if (e.key === 'ArrowUp') {
                e.preventDefault();
                if (allFilteredOptions.length > 0) {
                  setHighlightedIndex((prev) =>
                    prev > 0 ? prev - 1 : allFilteredOptions.length - 1,
                  );
                }
              } else if (e.key === 'ArrowLeft') {
                e.preventDefault();
                setOpen(false);
              } else if (e.key === 'Enter' && highlightedIndex >= 0) {
                e.preventDefault();
                const option = allFilteredOptions[highlightedIndex];
                if (option) {
                  const isSelected = effectiveValues.includes(option.value as T);
                  const next = isSelected
                    ? (effectiveValues.filter((v) => v !== option.value) as T[])
                    : isMultiSelect
                      ? ([...effectiveValues, option.value] as T[])
                      : ([option.value] as T[]);

                  if (
                    !isSelected &&
                    isMultiSelect &&
                    field.maxSelections &&
                    next.length > field.maxSelections
                  ) {
                    return;
                  }

                  if (field.onValueChange) {
                    field.onValueChange(next);
                  } else {
                    onChange(next);
                  }
                  if (!isMultiSelect) handleClose();
                }
              }
              e.stopPropagation();
            }}
          />
          <DropdownMenuSeparator />
        </>
      )}
      <div className="relative flex max-h-full">
        <div
          className="flex max-h-[min(var(--available-height),24rem)] w-full scroll-pt-2 scroll-pb-2 flex-col overscroll-contain"
          role="listbox"
          id={`${baseId}-listbox`}
        >
          <ScrollArea className="size-full min-h-0 **:data-[slot=scroll-area-scrollbar]:m-0 [&_[data-slot=scroll-area-viewport]]:h-full [&_[data-slot=scroll-area-viewport]]:overscroll-contain">
            {allFilteredOptions.length === 0 && (
              <div className="text-mauve-11 py-2 text-center text-sm">
                {context.i18n.noResultsFound}
              </div>
            )}

            {/* Selected items */}
            {filteredSelectedOptions.length > 0 && (
              <DropdownMenuGroup className="px-1">
                {filteredSelectedOptions.map((option, index) => {
                  const isHighlighted = highlightedIndex === index;
                  const itemId = `${baseId}-item-${index}`;

                  return (
                    <DropdownMenuCheckboxItem
                      key={String(option.value)}
                      id={itemId}
                      role="option"
                      aria-selected={isHighlighted}
                      data-highlighted={isHighlighted || undefined}
                      onMouseEnter={() => setHighlightedIndex(index)}
                      checked={true}
                      className={cn(
                        'data-highlighted:bg-mauve-4 data-highlighted:text-mauve-12',
                        option.className,
                      )}
                      onSelect={(e) => {
                        if (isMultiSelect) e.preventDefault();
                      }}
                      onCheckedChange={() => {
                        const next = effectiveValues.filter((v) => v !== option.value) as T[];
                        if (field.onValueChange) {
                          field.onValueChange(next);
                        } else {
                          onChange(next);
                        }
                        if (!isMultiSelect) handleClose();
                      }}
                    >
                      {option.icon && option.icon}
                      <span className="truncate">{option.label}</span>
                    </DropdownMenuCheckboxItem>
                  );
                })}
              </DropdownMenuGroup>
            )}

            {/* Separator */}
            {filteredSelectedOptions.length > 0 && filteredUnselectedOptions.length > 0 && (
              <DropdownMenuSeparator className="mx-0" />
            )}

            {/* Available items */}
            {filteredUnselectedOptions.length > 0 && (
              <DropdownMenuGroup className="px-1">
                {filteredUnselectedOptions.map((option, index) => {
                  const overallIndex = index + filteredSelectedOptions.length;
                  const isHighlighted = highlightedIndex === overallIndex;
                  const itemId = `${baseId}-item-${overallIndex}`;

                  return (
                    <DropdownMenuCheckboxItem
                      key={String(option.value)}
                      id={itemId}
                      role="option"
                      aria-selected={isHighlighted}
                      data-highlighted={isHighlighted || undefined}
                      onMouseEnter={() => setHighlightedIndex(overallIndex)}
                      checked={false}
                      className={cn(
                        'data-highlighted:bg-mauve-4 data-highlighted:text-mauve-12',
                        option.className,
                      )}
                      onSelect={(e) => {
                        if (isMultiSelect) e.preventDefault();
                      }}
                      onCheckedChange={() => {
                        const next = isMultiSelect
                          ? ([...effectiveValues, option.value] as T[])
                          : ([option.value] as T[]);

                        if (
                          isMultiSelect &&
                          field.maxSelections &&
                          next.length > field.maxSelections
                        ) {
                          return;
                        }

                        if (field.onValueChange) {
                          field.onValueChange(next);
                        } else {
                          onChange(next);
                        }
                        if (!isMultiSelect) handleClose();
                      }}
                    >
                      {option.icon && option.icon}
                      <span className="truncate">{option.label}</span>
                    </DropdownMenuCheckboxItem>
                  );
                })}
              </DropdownMenuGroup>
            )}
          </ScrollArea>
        </div>
      </div>
    </>
  );

  if (inline) {
    return <div className="w-full">{renderMenuContent()}</div>;
  }

  return (
    <DropdownMenu
      open={open}
      onOpenChange={(open) => {
        setOpen(open);
        if (!open) {
          setTimeout(() => setSearchInput(''), 200);
        }
      }}
    >
      <DropdownMenuTrigger
        render={
          <Button
            variant="outline"
            size={context.size}
            className={cn(
              'border-mauve-6 dark:border-mauve-6 text-mauve-12 shadow-none hover:bg-mauve-2 dark:hover:bg-mauve-2',
              context.size === 'sm' && 'text-xs px-2 h-7 min-h-0',
            )}
          >
            <div className="flex items-center gap-1.5">
              {field.customValueRenderer ? (
                field.customValueRenderer(values, field.options || [])
              ) : (
                <>
                  {selectedOptions.length > 0 && (
                    <div className="flex items-center -space-x-1.5">
                      {selectedOptions.slice(0, 3).map((option) => (
                        <div key={String(option.value)}>{option.icon}</div>
                      ))}
                    </div>
                  )}
                  {selectedOptions.length === 1
                    ? selectedOptions[0].label
                    : selectedOptions.length > 1
                      ? `${selectedOptions.length} ${context.i18n.selectedCount}`
                      : context.i18n.select}
                </>
              )}
            </div>
          </Button>
        }
      />
      <DropdownMenuContent align="start" className={cn('w-[200px] px-0', field.className)}>
        {renderMenuContent()}
      </DropdownMenuContent>
    </DropdownMenu>
  );
}

function FilterValueSelector<T = unknown>({
  field,
  values,
  onChange,
  operator,
  autoFocus,
}: FilterValueSelectorProps<T>) {
  // const context = useFilterContext()

  if (operator === 'empty' || operator === 'not_empty') {
    return null;
  }

  if (field.customRenderer) {
    return (
      <ButtonGroupText className="bg-mauve-2 dark:bg-mauve-2 border-mauve-6 dark:border-mauve-6 text-mauve-12 text-xs px-2 h-7 min-h-0 shadow-none hover:bg-mauve-3 dark:hover:bg-mauve-3 aria-expanded:bg-mauve-3 text-start whitespace-nowrap outline-hidden">
        {field.customRenderer({ field, values, onChange, operator })}
      </ButtonGroupText>
    );
  }

  if (field.type === 'text') {
    return (
      <FilterInput
        type="text"
        value={(values[0] as string) || ''}
        onChange={(e) => onChange([e.target.value] as T[])}
        placeholder={field.placeholder}
        pattern={field.pattern}
        field={field}
        className={cn('w-36', field.className)}
        autoFocus={autoFocus}
      />
    );
  }

  if (field.type === 'select' || field.type === 'multiselect') {
    return <SelectOptionsPopover field={field} values={values} onChange={onChange} />;
  }

  return <SelectOptionsPopover field={field} values={values} onChange={onChange} />;
}
export interface Filter<T = unknown> {
  id: string;
  field: string;
  operator: string;
  values: T[];
}

export interface FilterGroup<T = unknown> {
  id: string;
  label?: string;
  filters: Filter<T>[];
  fields: FilterFieldConfig<T>[];
}

interface FiltersContentProps<T = unknown> {
  filters: Filter<T>[];
  fields: FilterFieldsConfig<T>;
  onChange: (filters: Filter<T>[]) => void;
}

export const FiltersContent = <T = unknown>({
  filters,
  fields,
  onChange,
}: FiltersContentProps<T>) => {
  const context = useFilterContext();
  const fieldsMap = useMemo(() => getFieldsMap(fields), [fields]);

  const updateFilter = useCallback(
    (filterId: string, updates: Partial<Filter<T>>) => {
      onChange(
        filters.map((filter) => {
          if (filter.id === filterId) {
            const updatedFilter = { ...filter, ...updates };
            if (updates.operator === 'empty' || updates.operator === 'not_empty') {
              updatedFilter.values = [] as T[];
            }
            return updatedFilter;
          }
          return filter;
        }),
      );
    },
    [filters, onChange],
  );

  const removeFilter = useCallback(
    (filterId: string) => {
      onChange(filters.filter((filter) => filter.id !== filterId));
    },
    [filters, onChange],
  );

  return (
    <div
      className={cn(
        filtersContainerVariants({
          variant: context.variant,
          size: context.size,
        }),
        context.className,
      )}
    >
      {filters.map((filter) => {
        const field = fieldsMap[filter.field];
        if (!field) return null;

        return (
          <ButtonGroup key={filter.id}>
            <ButtonGroupText>
              {field.icon && field.icon}
              {field.label}
            </ButtonGroupText>

            <FilterOperatorDropdown<T>
              field={field}
              operator={filter.operator}
              values={filter.values}
              onChange={(operator) => updateFilter(filter.id, { operator })}
            />

            <FilterValueSelector<T>
              field={field}
              values={filter.values}
              onChange={(values) => updateFilter(filter.id, { values })}
              operator={filter.operator}
              autoFocus={false}
            />

            <FilterRemoveButton onClick={() => removeFilter(filter.id)} />
          </ButtonGroup>
        );
      })}
    </div>
  );
};

interface FiltersProps<T = unknown> {
  filters: Filter<T>[];
  fields: FilterFieldsConfig<T>;
  onChange: (filters: Filter<T>[]) => void;
  className?: string;
  variant?: 'solid' | 'default';
  size?: 'sm' | 'default' | 'lg';
  radius?: 'default' | 'full';
  i18n?: Partial<FilterI18nConfig>;
  showSearchInput?: boolean;
  trigger?: React.ReactNode;
  allowMultiple?: boolean;
  menuPopupClassName?: string;
  collapseAddButton?: boolean;
  enableShortcut?: boolean;
  shortcutKey?: string;
  shortcutLabel?: string;
}

interface FilterSubmenuContentProps<T = unknown> {
  field: FilterFieldConfig<T>;
  currentValues: T[];
  isMultiSelect: boolean;
  onToggle: (value: T, isSelected: boolean) => void;
  i18n: FilterI18nConfig;
  isActive?: boolean;
  onActive?: () => void;
  onBack?: () => void;
  onClose?: () => void;
}

function FilterSubmenuContent<T = unknown>({
  field,
  currentValues,
  isMultiSelect,
  onToggle,
  i18n,
  isActive,
  onActive,
  onBack,
  onClose,
}: FilterSubmenuContentProps<T>) {
  const [searchInput, setSearchInput] = useState('');
  const [highlightedIndex, setHighlightedIndex] = useState(-1);
  const inputRef = useRef<HTMLInputElement>(null);
  const baseId = useId();

  useEffect(() => {
    if (isActive) {
      if (field.searchable !== false) {
        inputRef.current?.focus();
      } else {
        const listbox = document.getElementById(`${baseId}-listbox`);
        listbox?.focus();
      }
    }
  }, [isActive, field.searchable, baseId]);

  // biome-ignore lint/correctness/useExhaustiveDependencies: searchInput é trigger intencional
  useEffect(() => {
    setHighlightedIndex(-1);
  }, [searchInput]);

  useEffect(() => {
    if (highlightedIndex >= 0 && isActive) {
      const element = document.getElementById(`${baseId}-item-${highlightedIndex}`);
      element?.scrollIntoView({ block: 'nearest' });
    }
  }, [highlightedIndex, isActive, baseId]);

  const filteredOptions = useMemo(() => {
    return (
      field.options?.filter((option) => {
        const isSelected = currentValues.includes(option.value);
        if (isSelected) return true;
        if (!searchInput) return true;
        return option.label.toLowerCase().includes(searchInput.toLowerCase());
      }) || []
    );
  }, [field.options, searchInput, currentValues]);

  useEffect(() => {
    if (isActive && filteredOptions.length > 0) {
      setHighlightedIndex(0);
    }
  }, [isActive, filteredOptions.length]);

  return (
    <div className="flex flex-col" onMouseEnter={onActive}>
      {field.searchable !== false && (
        <>
          <Input
            ref={inputRef}
            role="combobox"
            aria-autocomplete="list"
            aria-expanded={true}
            aria-haspopup="listbox"
            aria-controls={`${baseId}-listbox`}
            aria-activedescendant={
              highlightedIndex >= 0 ? `${baseId}-item-${highlightedIndex}` : undefined
            }
            placeholder={i18n.placeholders.searchField(field.label || '')}
            className={cn(
              'h-8 rounded-none border-0 bg-transparent! px-2 text-sm shadow-none',
              'focus-visible:border-mauve-7 focus-visible:ring-0 focus-visible:ring-offset-0',
              isActive && 'placeholder:text-mauve-12',
            )}
            value={searchInput}
            onBlur={() => isActive && inputRef.current?.focus()}
            onChange={(e) => setSearchInput(e.target.value)}
            onFocus={() => onActive?.()}
            onMouseEnter={(e) => {
              onActive?.();
              e.stopPropagation();
            }}
            onClick={(e) => e.stopPropagation()}
            onKeyDown={(e) => {
              if (e.key === 'ArrowDown') {
                e.preventDefault();
                if (filteredOptions.length > 0) {
                  setHighlightedIndex((prev) => (prev < filteredOptions.length - 1 ? prev + 1 : 0));
                }
              } else if (e.key === 'ArrowUp') {
                e.preventDefault();
                if (filteredOptions.length > 0) {
                  setHighlightedIndex((prev) => (prev > 0 ? prev - 1 : filteredOptions.length - 1));
                }
              } else if (e.key === 'ArrowLeft') {
                e.preventDefault();
                onBack?.();
              } else if (e.key === 'Enter' && highlightedIndex >= 0) {
                e.preventDefault();
                const option = filteredOptions[highlightedIndex];
                if (option) {
                  onToggle(option.value as T, currentValues.includes(option.value));
                  if (!isMultiSelect) {
                    onBack?.();
                  }
                }
              } else if (e.key === 'Escape') {
                e.preventDefault();
                onClose?.();
              }
              e.stopPropagation();
            }}
          />
          <DropdownMenuSeparator />
        </>
      )}
      <div className="relative flex max-h-full">
        <div
          className="flex max-h-[min(var(--available-height),24rem)] w-full scroll-pt-2 scroll-pb-2 flex-col overscroll-contain outline-hidden"
          role="listbox"
          id={`${baseId}-listbox`}
          tabIndex={field.searchable === false ? 0 : -1}
          onKeyDown={(e) => {
            if (field.searchable === false) {
              if (e.key === 'ArrowDown') {
                e.preventDefault();
                if (filteredOptions.length > 0) {
                  setHighlightedIndex((prev) => (prev < filteredOptions.length - 1 ? prev + 1 : 0));
                }
              } else if (e.key === 'ArrowUp') {
                e.preventDefault();
                if (filteredOptions.length > 0) {
                  setHighlightedIndex((prev) => (prev > 0 ? prev - 1 : filteredOptions.length - 1));
                }
              } else if (e.key === 'ArrowLeft') {
                e.preventDefault();
                onBack?.();
              } else if (e.key === 'Enter' && highlightedIndex >= 0) {
                e.preventDefault();
                const option = filteredOptions[highlightedIndex];
                if (option) {
                  onToggle(option.value as T, currentValues.includes(option.value));
                  if (!isMultiSelect) {
                    onBack?.();
                  }
                }
              } else if (e.key === 'Escape') {
                e.preventDefault();
                onClose?.();
              }
              e.stopPropagation();
            }
          }}
        >
          <ScrollArea className="size-full min-h-0 **:data-[slot=scroll-area-scrollbar]:m-0 [&_[data-slot=scroll-area-viewport]]:h-full [&_[data-slot=scroll-area-viewport]]:overscroll-contain">
            {filteredOptions.length === 0 ? (
              <div className="text-mauve-11 py-2 text-center text-sm">{i18n.noResultsFound}</div>
            ) : (
              <DropdownMenuGroup>
                {filteredOptions.map((option, index) => {
                  const isSelected = currentValues.includes(option.value);
                  const isHighlighted = highlightedIndex === index;
                  const itemId = `${baseId}-item-${index}`;

                  return (
                    <DropdownMenuCheckboxItem
                      key={String(option.value)}
                      id={itemId}
                      role="option"
                      aria-selected={isHighlighted}
                      data-highlighted={isHighlighted || undefined}
                      onMouseEnter={() => setHighlightedIndex(index)}
                      checked={isSelected}
                      className={cn(
                        'data-highlighted:bg-mauve-4 data-highlighted:text-mauve-12',
                        option.className,
                      )}
                      onSelect={(e) => {
                        if (isMultiSelect) e.preventDefault();
                      }}
                      onCheckedChange={() => onToggle(option.value as T, isSelected)}
                    >
                      {option.icon && option.icon}
                      <span className="truncate">{option.label}</span>
                    </DropdownMenuCheckboxItem>
                  );
                })}
              </DropdownMenuGroup>
            )}
          </ScrollArea>
        </div>
      </div>
    </div>
  );
}

export function Filters<T = unknown>({
  filters,
  fields,
  onChange,
  className,
  variant = 'default',
  size = 'default',
  radius = 'default',
  i18n,
  showSearchInput = true,
  trigger,
  allowMultiple = true,
  menuPopupClassName,
  enableShortcut = false,
  shortcutKey = 'f',
  shortcutLabel = 'F',
}: FiltersProps<T>) {
  const [addFilterOpen, setAddFilterOpen] = useState(false);
  const [menuSearchInput, setMenuSearchInput] = useState('');
  const [activeMenu, setActiveMenu] = useState<string>('root');
  const [openSubMenu, setOpenSubMenu] = useState<string | null>(null);
  const [highlightedIndex, setHighlightedIndex] = useState(-1);
  const [lastAddedFilterId, setLastAddedFilterId] = useState<string | null>(null);
  const rootInputRef = useRef<HTMLInputElement>(null);
  const rootId = useId();

  useEffect(() => {
    if (!enableShortcut) return;

    const handleKeyDown = (e: KeyboardEvent) => {
      if (
        e.key.toLowerCase() === shortcutKey.toLowerCase() &&
        !addFilterOpen &&
        !(
          document.activeElement instanceof HTMLInputElement ||
          document.activeElement instanceof HTMLTextAreaElement
        )
      ) {
        e.preventDefault();
        setAddFilterOpen(true);
      }
    };

    window.addEventListener('keydown', handleKeyDown);
    return () => window.removeEventListener('keydown', handleKeyDown);
  }, [enableShortcut, shortcutKey, addFilterOpen]);

  useEffect(() => {
    if (addFilterOpen && activeMenu === 'root') {
      rootInputRef.current?.focus();
    }
  }, [addFilterOpen, activeMenu]);

  // biome-ignore lint/correctness/useExhaustiveDependencies: menuSearchInput é trigger intencional
  useEffect(() => {
    setHighlightedIndex(-1);
  }, [menuSearchInput]);

  useEffect(() => {
    if (highlightedIndex >= 0 && addFilterOpen) {
      const element = document.getElementById(`${rootId}-item-${highlightedIndex}`);
      element?.scrollIntoView({ block: 'nearest' });
    }
  }, [highlightedIndex, addFilterOpen, rootId]);

  useEffect(() => {
    if (!addFilterOpen) {
      setOpenSubMenu(null);
    }
  }, [addFilterOpen]);

  // Track which filter instance is being built in the current Add Filter menu session
  // Maps fieldKey -> unique filterId created during this open session
  const [sessionFilterIds, setSessionFilterIds] = useState<Record<string, string>>({});

  useEffect(() => {
    if (!lastAddedFilterId) return;
    const timer = setTimeout(() => {
      setLastAddedFilterId(null);
    }, 1000);
    return () => clearTimeout(timer);
  }, [lastAddedFilterId]);

  const mergedI18n: FilterI18nConfig = {
    ...DEFAULT_I18N,
    ...i18n,
    operators: { ...DEFAULT_I18N.operators, ...i18n?.operators },
    placeholders: { ...DEFAULT_I18N.placeholders, ...i18n?.placeholders },
    validation: { ...DEFAULT_I18N.validation, ...i18n?.validation },
  };

  const fieldsMap = useMemo(() => getFieldsMap(fields), [fields]);

  const updateFilter = useCallback(
    (filterId: string, updates: Partial<Filter<T>>) => {
      onChange(
        filters.map((filter) => {
          if (filter.id === filterId) {
            const updatedFilter = { ...filter, ...updates };
            if (updates.operator === 'empty' || updates.operator === 'not_empty') {
              updatedFilter.values = [] as T[];
            }
            return updatedFilter;
          }
          return filter;
        }),
      );
    },
    [filters, onChange],
  );

  const removeFilter = useCallback(
    (filterId: string) => {
      onChange(filters.filter((filter) => filter.id !== filterId));
    },
    [filters, onChange],
  );

  const addFilter = useCallback(
    (fieldKey: string) => {
      const field = fieldsMap[fieldKey];
      if (field?.key) {
        const defaultOperator =
          field.defaultOperator || (field.type === 'multiselect' ? 'is_any_of' : 'is');
        const defaultValues: unknown[] = field.type === 'text' ? [''] : [];
        const newFilter = createFilter<T>(fieldKey, defaultOperator, defaultValues as T[]);
        setLastAddedFilterId(newFilter.id);
        onChange([...filters, newFilter]);
        setAddFilterOpen(false);
        setMenuSearchInput('');
      }
    },
    [fieldsMap, filters, onChange],
  );

  useEffect(() => {
    if (addFilterOpen && activeMenu === 'root') {
      rootInputRef.current?.focus();
    }
  }, [addFilterOpen, activeMenu]);

  const selectableFields = useMemo(() => {
    const flatFields = flattenFields(fields);
    return flatFields.filter((field) => {
      if (!field.key || field.type === 'separator') return false;
      if (allowMultiple) return true;
      return !filters.some((filter) => filter.field === field.key);
    });
  }, [fields, filters, allowMultiple]);

  const filteredFields = useMemo(() => {
    return selectableFields.filter(
      (f) => !menuSearchInput || f.label?.toLowerCase().includes(menuSearchInput.toLowerCase()),
    );
  }, [selectableFields, menuSearchInput]);

  useEffect(() => {
    if (addFilterOpen && filteredFields.length > 0) {
      setHighlightedIndex(0);
    }
  }, [addFilterOpen, filteredFields.length]);

  const triggerButton = useRender({
    render: trigger as React.ReactElement,
    defaultTagName: 'button',
  });

  return (
    <FilterContext.Provider
      value={{
        variant,
        size,
        radius,
        i18n: mergedI18n,
        className,
        trigger,
        allowMultiple,
      }}
    >
      <div className={cn(filtersContainerVariants({ variant, size }), className)}>
        {selectableFields.length > 0 && (
          <DropdownMenu
            open={addFilterOpen}
            onOpenChange={(open) => {
              setAddFilterOpen(open);
              if (!open) {
                setMenuSearchInput('');
                setSessionFilterIds({});
              } else {
                setActiveMenu('root');
              }
            }}
          >
            <DropdownMenuTrigger render={triggerButton} />
            <DropdownMenuContent className={cn('w-[220px]', menuPopupClassName)} align="start">
              {showSearchInput && (
                <>
                  <div className="relative">
                    <Input
                      ref={rootInputRef}
                      role="combobox"
                      aria-controls={`${rootId}-listbox`}
                      aria-activedescendant={
                        highlightedIndex >= 0 ? `${rootId}-item-${highlightedIndex}` : undefined
                      }
                      placeholder={mergedI18n.searchFields}
                      className={cn(
                        'h-8 rounded-none border-0 bg-transparent! px-2 text-sm shadow-none',
                        'focus-visible:border-mauve-7 focus-visible:ring-0 focus-visible:ring-offset-0',
                        activeMenu === 'root' && 'placeholder:text-mauve-12',
                      )}
                      value={menuSearchInput}
                      onFocus={() => setActiveMenu('root')}
                      onMouseEnter={() => setActiveMenu('root')}
                      onBlur={() => activeMenu === 'root' && rootInputRef.current?.focus()}
                      onChange={(e) => setMenuSearchInput(e.target.value)}
                      onClick={(e) => e.stopPropagation()}
                      onKeyDown={(e) => {
                        if (e.key === 'ArrowDown') {
                          e.preventDefault();
                          if (filteredFields.length > 0) {
                            setHighlightedIndex((prev) =>
                              prev < filteredFields.length - 1 ? prev + 1 : 0,
                            );
                          }
                        } else if (e.key === 'ArrowUp') {
                          e.preventDefault();
                          if (filteredFields.length > 0) {
                            setHighlightedIndex((prev) =>
                              prev > 0 ? prev - 1 : filteredFields.length - 1,
                            );
                          }
                        } else if (
                          (e.key === 'ArrowRight' || e.key === 'ArrowLeft') &&
                          highlightedIndex >= 0
                        ) {
                          const field = filteredFields[highlightedIndex];
                          const hasSubMenu =
                            field &&
                            (field.type === 'select' || field.type === 'multiselect') &&
                            field.options?.length;

                          if (e.key === 'ArrowRight' && hasSubMenu) {
                            e.preventDefault();
                            setOpenSubMenu(field.key || null);
                            setActiveMenu(field.key || 'root');
                          } else if (e.key === 'ArrowLeft') {
                            e.preventDefault();
                            if (openSubMenu) {
                              setOpenSubMenu(null);
                              setActiveMenu('root');
                            }
                          }
                        } else if (e.key === 'Enter' && highlightedIndex >= 0) {
                          e.preventDefault();
                          const field = filteredFields[highlightedIndex];
                          if (field.key) {
                            const hasSubMenu =
                              (field.type === 'select' || field.type === 'multiselect') &&
                              field.options?.length;
                            if (!hasSubMenu) {
                              addFilter(field.key);
                            } else {
                              if (openSubMenu === field.key) {
                                setOpenSubMenu(null);
                                setActiveMenu('root');
                              } else {
                                setOpenSubMenu(field.key);
                                setActiveMenu(field.key);
                              }
                            }
                          }
                        } else if (e.key === 'Escape') {
                          setAddFilterOpen(false);
                        }
                        e.stopPropagation();
                      }}
                    />
                    {enableShortcut && shortcutLabel && (
                      <Kbd className="bg-mauve-1 absolute top-1/2 right-2 -translate-y-1/2 border">
                        {shortcutLabel}
                      </Kbd>
                    )}
                  </div>
                  <DropdownMenuSeparator />
                </>
              )}

              <div className="relative flex max-h-full">
                <div
                  className="flex max-h-[min(var(--available-height),24rem)] w-full scroll-pt-2 scroll-pb-2 flex-col overscroll-contain"
                  role="listbox"
                  tabIndex={-1}
                  id={`${rootId}-listbox`}
                  onMouseEnter={() => setActiveMenu('root')}
                >
                  <ScrollArea className="**:data-[slot=scroll-area-scrollbar]:m-0">
                    {(() => {
                      if (filteredFields.length === 0) {
                        return (
                          <div className="text-mauve-11 py-2 text-center text-sm">
                            {mergedI18n.noFieldsFound}
                          </div>
                        );
                      }

                      return filteredFields.map((field, index) => {
                        const isHighlighted = highlightedIndex === index;
                        const itemId = `${rootId}-item-${index}`;
                        const hasSubMenu =
                          (field.type === 'select' || field.type === 'multiselect') &&
                          field.options?.length;

                        if (hasSubMenu) {
                          const isMultiSelect = field.type === 'multiselect';
                          const fieldKey = field.key as string;
                          const sessionFilterId = sessionFilterIds[fieldKey];
                          const sessionFilter = sessionFilterId
                            ? filters.find((f) => f.id === sessionFilterId)
                            : null;
                          const currentValues = sessionFilter?.values || [];

                          return (
                            <DropdownMenuSub
                              key={fieldKey}
                              open={openSubMenu === fieldKey}
                              onOpenChange={(open) => {
                                if (open) {
                                  setOpenSubMenu(fieldKey);
                                } else {
                                  if (openSubMenu === fieldKey) {
                                    setOpenSubMenu(null);
                                    setActiveMenu('root');
                                  }
                                }
                              }}
                            >
                              <DropdownMenuSubTrigger
                                id={itemId}
                                role="option"
                                aria-selected={isHighlighted}
                                data-highlighted={isHighlighted || undefined}
                                onMouseEnter={() => {
                                  setHighlightedIndex(index);
                                  setActiveMenu('root');
                                }}
                                className="data-popup-open:bg-mauve-4 data-popup-open:text-mauve-12 data-highlighted:bg-mauve-4 data-highlighted:text-mauve-12"
                              >
                                {field.icon}
                                <span>{field.label}</span>
                              </DropdownMenuSubTrigger>
                              <DropdownMenuSubContent className="w-[200px]" side="right">
                                <FilterSubmenuContent
                                  field={field}
                                  currentValues={currentValues}
                                  isMultiSelect={isMultiSelect}
                                  i18n={mergedI18n}
                                  isActive={activeMenu === fieldKey}
                                  onActive={() => {
                                    if (field.searchable !== false) {
                                      setActiveMenu(fieldKey);
                                    }
                                  }}
                                  onBack={() => {
                                    setOpenSubMenu(null);
                                    setActiveMenu('root');
                                  }}
                                  onClose={() => setAddFilterOpen(false)}
                                  onToggle={(value, isSelected) => {
                                    if (isMultiSelect) {
                                      const nextValues = isSelected
                                        ? (currentValues.filter((v) => v !== value) as T[])
                                        : ([...currentValues, value] as T[]);

                                      if (sessionFilter) {
                                        if (nextValues.length === 0) {
                                          onChange(
                                            filters.filter((f) => f.id !== sessionFilter.id),
                                          );
                                          setSessionFilterIds((prev) => ({
                                            ...prev,
                                            [fieldKey]: '',
                                          }));
                                        } else {
                                          onChange(
                                            filters.map((f) =>
                                              f.id === sessionFilter.id
                                                ? { ...f, values: nextValues }
                                                : f,
                                            ),
                                          );
                                        }
                                      } else {
                                        const newFilter = createFilter<T>(
                                          fieldKey,
                                          field.defaultOperator || 'is_any_of',
                                          nextValues,
                                        );
                                        onChange([...filters, newFilter]);
                                        setSessionFilterIds((prev) => ({
                                          ...prev,
                                          [fieldKey]: newFilter.id,
                                        }));
                                      }
                                    } else {
                                      const newFilter = createFilter<T>(
                                        fieldKey,
                                        field.defaultOperator || 'is',
                                        [value] as T[],
                                      );
                                      setLastAddedFilterId(newFilter.id);
                                      onChange([...filters, newFilter]);
                                      setAddFilterOpen(false);
                                    }
                                  }}
                                />
                              </DropdownMenuSubContent>
                            </DropdownMenuSub>
                          );
                        }

                        return (
                          <DropdownMenuItem
                            key={field.key}
                            id={itemId}
                            role="option"
                            aria-selected={isHighlighted}
                            data-highlighted={isHighlighted || undefined}
                            onMouseEnter={() => setHighlightedIndex(index)}
                            onClick={() => field.key && addFilter(field.key)}
                            className="data-highlighted:bg-mauve-4 data-highlighted:text-mauve-12"
                          >
                            {field.icon}
                            <span>{field.label}</span>
                          </DropdownMenuItem>
                        );
                      });
                    })()}
                  </ScrollArea>
                </div>
              </div>
            </DropdownMenuContent>
          </DropdownMenu>
        )}

        {filters.map((filter) => {
          const field = fieldsMap[filter.field];
          if (!field) return null;
          return (
            <ButtonGroup key={filter.id} className="h-7 items-stretch">
              <ButtonGroupText className="bg-mauve-3 dark:bg-mauve-3 border-mauve-6 dark:border-mauve-6 text-mauve-11 text-xs px-2 h-7 min-h-0 gap-1 shadow-none">
                {field.icon && field.icon}
                {field.label}
              </ButtonGroupText>
              <FilterOperatorDropdown<T>
                field={field}
                operator={filter.operator}
                values={filter.values}
                onChange={(operator) => updateFilter(filter.id, { operator })}
              />
              <FilterValueSelector<T>
                field={field}
                values={filter.values}
                operator={filter.operator}
                onChange={(values) => updateFilter(filter.id, { values })}
                autoFocus={filter.id === lastAddedFilterId}
              />
              <FilterRemoveButton onClick={() => removeFilter(filter.id)} />
            </ButtonGroup>
          );
        })}
      </div>
    </FilterContext.Provider>
  );
}

export const createFilter = <T = unknown>(
  field: string,
  operator?: string,
  values: T[] = [],
): Filter<T> => ({
  id: `${Date.now()}-${Math.random().toString(36).substring(2, 11)}`,
  field,
  operator: operator || 'is',
  values,
});

export const createFilterGroup = <T = unknown>(
  id: string,
  label: string,
  fields: FilterFieldConfig<T>[],
  initialFilters: Filter<T>[] = [],
): FilterGroup<T> => ({
  id,
  label,
  filters: initialFilters,
  fields,
});
```

---

## 📂 `kanban.tsx`

Caminho original no repositório: `/packages/ui/src/components/kanban.tsx`

```tsx
/* eslint-disable @typescript-eslint/no-explicit-any */
'use client';

import {
  DndContext,
  type DragEndEvent,
  type DragOverEvent,
  DragOverlay,
  type DragStartEvent,
  type DraggableAttributes,
  type DraggableSyntheticListeners,
  type DropAnimation,
  KeyboardSensor,
  PointerSensor,
  type UniqueIdentifier,
  defaultDropAnimation,
  defaultDropAnimationSideEffects,
  useDndContext as useDndCoreContext,
  useDndMonitor as useDndCoreMonitor,
  useSensor,
  useSensors,
} from '@dnd-kit/core';
import {
  SortableContext,
  arrayMove,
  rectSortingStrategy,
  sortableKeyboardCoordinates,
  useSortable,
  verticalListSortingStrategy,
} from '@dnd-kit/sortable';
import { CSS } from '@dnd-kit/utilities';
import { cn } from '@manager/lib';
import { Slot } from '@radix-ui/react-slot';
import * as React from 'react';

interface KanbanContextProps<T> {
  columns: Record<string, T[]>;
  setColumns: (columns: Record<string, T[]>) => void;
  getItemId: (item: T) => string;
  columnIds: string[];
  activeId: UniqueIdentifier | null;
  setActiveId: (id: UniqueIdentifier | null) => void;
  findContainer: (id: UniqueIdentifier) => string | undefined;
  isColumn: (id: UniqueIdentifier) => boolean;
}

export const useDndContext = useDndCoreContext;
export const useDndMonitor = useDndCoreMonitor;

const KanbanContext = React.createContext<KanbanContextProps<any>>({
  columns: {},
  setColumns: () => {},
  getItemId: () => '',
  columnIds: [],
  activeId: null,
  setActiveId: () => {},
  findContainer: () => undefined,
  isColumn: () => false,
});

const ColumnContext = React.createContext<{
  attributes: DraggableAttributes;
  listeners: DraggableSyntheticListeners | undefined;
  isDragging?: boolean;
  disabled?: boolean;
}>({
  attributes: {} as DraggableAttributes,
  listeners: undefined,
  isDragging: false,
  disabled: false,
});

const ItemContext = React.createContext<{
  listeners: DraggableSyntheticListeners | undefined;
  isDragging?: boolean;
  disabled?: boolean;
}>({
  listeners: undefined,
  isDragging: false,
  disabled: false,
});

const dropAnimationConfig: DropAnimation = {
  ...defaultDropAnimation,
  sideEffects: defaultDropAnimationSideEffects({
    styles: {
      active: {
        opacity: '0.4',
      },
    },
  }),
};

export interface KanbanMoveEvent {
  event: DragEndEvent;
  activeContainer: string;
  activeIndex: number;
  overContainer: string;
  overIndex: number;
}

export interface KanbanRootProps<T> {
  value: Record<string, T[]>;
  onValueChange: (value: Record<string, T[]>) => void;
  getItemValue: (item: T) => string;
  children: React.ReactNode;
  className?: string;
  onMove?: (event: KanbanMoveEvent) => void;
}

function Kanban<T>({
  value,
  onValueChange,
  getItemValue,
  children,
  className,
  onMove,
}: KanbanRootProps<T>) {
  const columns = value;
  const setColumns = onValueChange;
  const [activeId, setActiveId] = React.useState<UniqueIdentifier | null>(null);

  const sensors = useSensors(
    useSensor(PointerSensor, {
      activationConstraint: {
        distance: 10,
      },
    }),
    useSensor(KeyboardSensor, {
      coordinateGetter: sortableKeyboardCoordinates,
    }),
  );

  const columnIds = React.useMemo(() => Object.keys(columns), [columns]);

  const isColumn = React.useCallback(
    (id: UniqueIdentifier) => columnIds.includes(id as string),
    [columnIds],
  );

  const findContainer = React.useCallback(
    (id: UniqueIdentifier) => {
      if (isColumn(id)) return id as string;
      return columnIds.find((key) => columns[key].some((item) => getItemValue(item) === id));
    },
    [columns, columnIds, getItemValue, isColumn],
  );

  const handleDragStart = React.useCallback((event: DragStartEvent) => {
    setActiveId(event.active.id);
  }, []);

  const handleDragOver = React.useCallback(
    (event: DragOverEvent) => {
      if (onMove) {
        return;
      }

      const { active, over } = event;
      if (!over) return;

      if (isColumn(active.id)) return;

      const activeContainer = findContainer(active.id);
      const overContainer = findContainer(over.id);

      // Only handle moving items between different columns
      if (!activeContainer || !overContainer || activeContainer === overContainer) {
        return;
      }

      const activeItems = columns[activeContainer];
      const overItems = columns[overContainer];

      const activeIndex = activeItems.findIndex((item: T) => getItemValue(item) === active.id);
      let overIndex = overItems.findIndex((item: T) => getItemValue(item) === over.id);

      // If dropping on the column itself, not an item
      if (isColumn(over.id)) {
        overIndex = overItems.length;
      }

      const newOverItems = [...overItems];
      const [movedItem] = activeItems.splice(activeIndex, 1);
      newOverItems.splice(overIndex, 0, movedItem);

      setColumns({
        ...columns,
        [activeContainer]: [...activeItems],
        [overContainer]: newOverItems,
      });
    },
    [findContainer, getItemValue, isColumn, setColumns, columns, onMove],
  );

  const handleDragEnd = React.useCallback(
    (event: DragEndEvent) => {
      const { active, over } = event;
      setActiveId(null);

      if (!over) return;

      // Handle item move callback
      if (onMove && !isColumn(active.id)) {
        const activeContainer = findContainer(active.id);
        const overContainer = findContainer(over.id);

        if (activeContainer && overContainer) {
          const activeIndex = columns[activeContainer].findIndex(
            (item: T) => getItemValue(item) === active.id,
          );
          const overIndex = isColumn(over.id)
            ? columns[overContainer].length
            : columns[overContainer].findIndex((item: T) => getItemValue(item) === over.id);

          onMove({
            event,
            activeContainer,
            activeIndex,
            overContainer,
            overIndex,
          });
        }
        return;
      }

      // Handle column reordering
      if (isColumn(active.id) && isColumn(over.id)) {
        const activeIndex = columnIds.indexOf(active.id as string);
        const overIndex = columnIds.indexOf(over.id as string);
        if (activeIndex !== overIndex) {
          const newOrder = arrayMove(Object.keys(columns), activeIndex, overIndex);
          const newColumns: Record<string, T[]> = {};
          for (const key of newOrder) {
            newColumns[key] = columns[key];
          }

          setColumns(newColumns);
        }
        return;
      }

      const activeContainer = findContainer(active.id);
      const overContainer = findContainer(over.id);

      // Handle item reordering within the same column
      if (activeContainer && overContainer && activeContainer === overContainer) {
        const container = activeContainer;
        const activeIndex = columns[container].findIndex(
          (item: T) => getItemValue(item) === active.id,
        );
        const overIndex = columns[container].findIndex((item: T) => getItemValue(item) === over.id);

        if (activeIndex !== overIndex) {
          setColumns({
            ...columns,
            [container]: arrayMove(columns[container], activeIndex, overIndex),
          });
        }
      }
    },
    [columnIds, columns, findContainer, getItemValue, isColumn, setColumns, onMove],
  );

  const contextValue = React.useMemo(
    () => ({
      columns,
      setColumns,
      getItemId: getItemValue,
      columnIds,
      activeId,
      setActiveId,
      findContainer,
      isColumn,
    }),
    [columns, setColumns, getItemValue, columnIds, activeId, findContainer, isColumn],
  );

  return (
    <KanbanContext.Provider value={contextValue}>
      <DndContext
        sensors={sensors}
        onDragStart={handleDragStart}
        onDragOver={handleDragOver}
        onDragEnd={handleDragEnd}
      >
        <div data-slot="kanban" data-dragging={activeId !== null} className={cn(className)}>
          {children}
        </div>
      </DndContext>
    </KanbanContext.Provider>
  );
}

export interface KanbanBoardProps {
  className?: string;
  children: React.ReactNode;
}

function KanbanBoard({ children, className }: KanbanBoardProps) {
  const { columnIds } = React.useContext(KanbanContext);

  return (
    <SortableContext items={columnIds} strategy={rectSortingStrategy}>
      <div
        data-slot="kanban-board"
        className={cn('grid auto-rows-fr sm:grid-cols-3 gap-4', className)}
      >
        {children}
      </div>
    </SortableContext>
  );
}

export interface KanbanColumnProps {
  value: string;
  className?: string;
  children: React.ReactNode;
  disabled?: boolean;
}

function KanbanColumn({ value, className, children, disabled }: KanbanColumnProps) {
  const {
    setNodeRef,
    transform,
    transition,
    attributes,
    listeners,
    isDragging: isSortableDragging,
  } = useSortable({
    id: value,
    disabled,
  });

  const { activeId, isColumn } = React.useContext(KanbanContext);
  const isColumnDragging = activeId ? isColumn(activeId) : false;

  const style = {
    transition,
    transform: CSS.Translate.toString(transform),
  } as React.CSSProperties;

  return (
    <ColumnContext.Provider
      value={{ attributes, listeners, isDragging: isColumnDragging, disabled }}
    >
      <div
        data-slot="kanban-column"
        data-value={value}
        data-dragging={isSortableDragging}
        data-disabled={disabled}
        ref={setNodeRef}
        style={style}
        className={cn(
          'group/kanban-column flex flex-col',
          isSortableDragging && 'opacity-50',
          disabled && 'opacity-50',
          className,
        )}
      >
        {children}
      </div>
    </ColumnContext.Provider>
  );
}

export interface KanbanColumnHandleProps {
  asChild?: boolean;
  className?: string;
  children?: React.ReactNode;
  cursor?: boolean;
}

function KanbanColumnHandle({
  asChild,
  className,
  children,
  cursor = true,
}: KanbanColumnHandleProps) {
  const { attributes, listeners, isDragging, disabled } = React.useContext(ColumnContext);

  const Comp = asChild ? Slot : 'div';

  return (
    <Comp
      data-slot="kanban-column-handle"
      data-dragging={isDragging}
      data-disabled={disabled}
      {...attributes}
      {...listeners}
      className={cn(
        'opacity-0 transition-opacity group-hover/kanban-column:opacity-100',
        cursor && (isDragging ? '!cursor-grabbing' : '!cursor-grab'),
        className,
      )}
    >
      {children}
    </Comp>
  );
}

export interface KanbanItemProps {
  value: string;
  asChild?: boolean;
  className?: string;
  children: React.ReactNode;
  disabled?: boolean;
}

function KanbanItem({ value, asChild = false, className, children, disabled }: KanbanItemProps) {
  const {
    setNodeRef,
    transform,
    transition,
    attributes,
    listeners,
    isDragging: isSortableDragging,
  } = useSortable({
    id: value,
    disabled,
  });

  const { activeId, isColumn } = React.useContext(KanbanContext);
  const isItemDragging = activeId ? !isColumn(activeId) : false;

  const style = {
    transition,
    transform: CSS.Translate.toString(transform),
  } as React.CSSProperties;

  const Comp = asChild ? Slot : 'div';

  return (
    <ItemContext.Provider value={{ listeners, isDragging: isItemDragging, disabled }}>
      <Comp
        data-slot="kanban-item"
        data-value={value}
        data-dragging={isSortableDragging}
        data-disabled={disabled}
        ref={setNodeRef}
        style={style}
        {...attributes}
        className={cn(isSortableDragging && 'opacity-50', disabled && 'opacity-50', className)}
      >
        {children}
      </Comp>
    </ItemContext.Provider>
  );
}

export interface KanbanItemHandleProps {
  asChild?: boolean;
  className?: string;
  children?: React.ReactNode;
  cursor?: boolean;
}

function KanbanItemHandle({ asChild, className, children, cursor = true }: KanbanItemHandleProps) {
  const { listeners, isDragging, disabled } = React.useContext(ItemContext);

  const Comp = asChild ? Slot : 'div';

  return (
    <Comp
      data-slot="kanban-item-handle"
      data-dragging={isDragging}
      data-disabled={disabled}
      {...listeners}
      className={cn(cursor && (isDragging ? '!cursor-grabbing' : '!cursor-grab'), className)}
    >
      {children}
    </Comp>
  );
}

export interface KanbanColumnContentProps {
  value: string;
  className?: string;
  children: React.ReactNode;
}

function KanbanColumnContent({ value, className, children }: KanbanColumnContentProps) {
  const { columns, getItemId } = React.useContext(KanbanContext);

  const itemIds = React.useMemo(() => columns[value].map(getItemId), [columns, getItemId, value]);

  return (
    <SortableContext items={itemIds} strategy={verticalListSortingStrategy}>
      <div data-slot="kanban-column-content" className={cn('flex flex-col gap-2', className)}>
        {children}
      </div>
    </SortableContext>
  );
}

export interface KanbanOverlayProps {
  className?: string;
  children?:
    | React.ReactNode
    | ((params: { value: UniqueIdentifier; variant: 'column' | 'item' }) => React.ReactNode);
}

function KanbanOverlay({ children, className }: KanbanOverlayProps) {
  const { activeId, isColumn } = React.useContext(KanbanContext);
  const [dimensions, setDimensions] = React.useState<{ width: number; height: number } | null>(
    null,
  );

  React.useEffect(() => {
    if (activeId) {
      const element = document.querySelector(
        `[data-slot="kanban-${isColumn(activeId) ? 'column' : 'item'}"][data-value="${activeId}"]`,
      );
      if (element) {
        const rect = element.getBoundingClientRect();
        setDimensions({ width: rect.width, height: rect.height });
      }
    } else {
      setDimensions(null);
    }
  }, [activeId, isColumn]);

  const style = {
    width: dimensions?.width,
    height: dimensions?.height,
  } as React.CSSProperties;

  const content = React.useMemo(() => {
    if (!activeId) return null;
    if (typeof children === 'function') {
      return children({
        value: activeId,
        variant: isColumn(activeId) ? 'column' : 'item',
      });
    }
    return children;
  }, [activeId, children, isColumn]);

  return (
    <DragOverlay dropAnimation={dropAnimationConfig}>
      <div
        data-slot="kanban-overlay"
        data-dragging={true}
        style={style}
        className={cn('pointer-events-none', className, activeId ? '!cursor-grabbing' : '')}
      >
        {content}
      </div>
    </DragOverlay>
  );
}

export {
  Kanban,
  KanbanBoard,
  KanbanColumn,
  KanbanColumnContent,
  KanbanColumnHandle,
  KanbanItem,
  KanbanItemHandle,
  KanbanOverlay,
};
```

---

## 📂 `map.tsx`

Caminho original no repositório: `/packages/ui/src/components/map.tsx`

```tsx
'use client';

import MapLibreGL, { type PopupOptions, type MarkerOptions } from 'maplibre-gl';
import 'maplibre-gl/dist/maplibre-gl.css';
import { Loader2, Locate, Maximize, Minus, Plus, X } from 'lucide-react';
import { useTheme } from 'next-themes';
import {
  type ReactNode,
  createContext,
  forwardRef,
  useCallback,
  useContext,
  useEffect,
  useId,
  useImperativeHandle,
  useMemo,
  useRef,
  useState,
} from 'react';
import { createPortal } from 'react-dom';

import { cn } from '@manager/lib';
import type React from 'react';

type MapContextValue = {
  map: MapLibreGL.Map | null;
  isLoaded: boolean;
};

const MapContext = createContext<MapContextValue | null>(null);

function useMap() {
  const context = useContext(MapContext);
  if (!context) {
    throw new Error('useMap must be used within a Map component');
  }
  return context;
}

const defaultStyles = {
  dark: 'https://basemaps.cartocdn.com/gl/dark-matter-gl-style/style.json',
  light: 'https://basemaps.cartocdn.com/gl/positron-gl-style/style.json',
};

type MapStyleOption = string | MapLibreGL.StyleSpecification;

type MapProps = {
  children?: ReactNode;
  /** Custom map styles for light and dark themes. Overrides the default Carto styles. */
  styles?: {
    light?: MapStyleOption;
    dark?: MapStyleOption;
  };
} & Omit<MapLibreGL.MapOptions, 'container' | 'style'>;

type MapRef = MapLibreGL.Map;

const DefaultLoader = () => (
  <div className="absolute inset-0 flex items-center justify-center">
    <div className="flex gap-1">
      <span className="size-1.5 rounded-full bg-mauve-9 animate-pulse" />
      <span className="size-1.5 rounded-full bg-mauve-9 animate-pulse [animation-delay:150ms]" />
      <span className="size-1.5 rounded-full bg-mauve-9 animate-pulse [animation-delay:300ms]" />
    </div>
  </div>
);

const MapRoot = forwardRef<MapRef, MapProps>(function MapRoot({ children, styles, ...props }, ref) {
  const containerRef = useRef<HTMLDivElement>(null);
  const [mapInstance, setMapInstance] = useState<MapLibreGL.Map | null>(null);
  const [isLoaded, setIsLoaded] = useState(false);
  const [isStyleLoaded, setIsStyleLoaded] = useState(false);
  const { resolvedTheme } = useTheme();
  const currentStyleRef = useRef<MapStyleOption | null>(null);

  const mapStyles = useMemo(
    () => ({
      dark: styles?.dark ?? defaultStyles.dark,
      light: styles?.light ?? defaultStyles.light,
    }),
    [styles],
  );

  useImperativeHandle(ref, () => mapInstance as MapLibreGL.Map, [mapInstance]);

  useEffect(() => {
    if (!containerRef.current) return;

    const initialStyle = resolvedTheme === 'dark' ? mapStyles.dark : mapStyles.light;
    currentStyleRef.current = initialStyle;

    const map = new MapLibreGL.Map({
      container: containerRef.current,
      style: initialStyle,
      renderWorldCopies: false,
      attributionControl: {
        compact: true,
      },
      ...props,
    });

    const styleDataHandler = () => setIsStyleLoaded(true);
    const loadHandler = () => setIsLoaded(true);

    map.on('load', loadHandler);
    map.on('styledata', styleDataHandler);
    setMapInstance(map);

    return () => {
      map.off('load', loadHandler);
      map.off('styledata', styleDataHandler);
      map.remove();
      setIsLoaded(false);
      setIsStyleLoaded(false);
      setMapInstance(null);
    };
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  useEffect(() => {
    if (!mapInstance || !resolvedTheme) return;

    const newStyle = resolvedTheme === 'dark' ? mapStyles.dark : mapStyles.light;

    if (currentStyleRef.current === newStyle) return;

    currentStyleRef.current = newStyle;
    setIsStyleLoaded(false);

    const frameId = requestAnimationFrame(() => {
      mapInstance.setStyle(newStyle, { diff: true });
    });

    return () => cancelAnimationFrame(frameId);
  }, [mapInstance, resolvedTheme, mapStyles]);

  const isLoading = !isLoaded || !isStyleLoaded;

  const contextValue = useMemo(
    () => ({
      map: mapInstance,
      isLoaded: isLoaded && isStyleLoaded,
    }),
    [mapInstance, isLoaded, isStyleLoaded],
  );

  return (
    <MapContext.Provider value={contextValue}>
      <div ref={containerRef} className="relative w-full h-full">
        {isLoading && <DefaultLoader />}
        {/* SSR-safe: children render only when map is loaded on client */}
        {mapInstance && children}
      </div>
    </MapContext.Provider>
  );
});

type MarkerContextValue = {
  marker: MapLibreGL.Marker;
  map: MapLibreGL.Map | null;
};

const MarkerContext = createContext<MarkerContextValue | null>(null);

function useMarkerContext() {
  const context = useContext(MarkerContext);
  if (!context) {
    throw new Error('Marker components must be used within MapMarker');
  }
  return context;
}

type MapMarkerProps = {
  /** Longitude coordinate for marker position */
  longitude: number;
  /** Latitude coordinate for marker position */
  latitude: number;
  /** Marker subcomponents (MarkerContent, MarkerPopup, MarkerTooltip, MarkerLabel) */
  children: ReactNode;
  /** Callback when marker is clicked */
  onClick?: (e: MouseEvent) => void;
  /** Callback when mouse enters marker */
  onMouseEnter?: (e: MouseEvent) => void;
  /** Callback when mouse leaves marker */
  onMouseLeave?: (e: MouseEvent) => void;
  /** Callback when marker drag starts (requires draggable: true) */
  onDragStart?: (lngLat: { lng: number; lat: number }) => void;
  /** Callback during marker drag (requires draggable: true) */
  onDrag?: (lngLat: { lng: number; lat: number }) => void;
  /** Callback when marker drag ends (requires draggable: true) */
  onDragEnd?: (lngLat: { lng: number; lat: number }) => void;
} & Omit<MarkerOptions, 'element'>;

function MapMarker({
  longitude,
  latitude,
  children,
  onClick,
  onMouseEnter,
  onMouseLeave,
  onDragStart,
  onDrag,
  onDragEnd,
  draggable = false,
  ...markerOptions
}: MapMarkerProps) {
  const { map } = useMap();

  const marker = useMemo(() => {
    const markerInstance = new MapLibreGL.Marker({
      ...markerOptions,
      element: document.createElement('div'),
      draggable,
    }).setLngLat([longitude, latitude]);

    const handleClick = (e: MouseEvent) => onClick?.(e);
    const handleMouseEnter = (e: MouseEvent) => onMouseEnter?.(e);
    const handleMouseLeave = (e: MouseEvent) => onMouseLeave?.(e);

    markerInstance.getElement()?.addEventListener('click', handleClick);
    markerInstance.getElement()?.addEventListener('mouseenter', handleMouseEnter);
    markerInstance.getElement()?.addEventListener('mouseleave', handleMouseLeave);

    const handleDragStart = () => {
      const lngLat = markerInstance.getLngLat();
      onDragStart?.({ lng: lngLat.lng, lat: lngLat.lat });
    };
    const handleDrag = () => {
      const lngLat = markerInstance.getLngLat();
      onDrag?.({ lng: lngLat.lng, lat: lngLat.lat });
    };
    const handleDragEnd = () => {
      const lngLat = markerInstance.getLngLat();
      onDragEnd?.({ lng: lngLat.lng, lat: lngLat.lat });
    };

    markerInstance.on('dragstart', handleDragStart);
    markerInstance.on('drag', handleDrag);
    markerInstance.on('dragend', handleDragEnd);

    return markerInstance;

    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  useEffect(() => {
    if (!map) return;

    marker.addTo(map);

    return () => {
      marker.remove();
    };

    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [map]);

  if (marker.getLngLat().lng !== longitude || marker.getLngLat().lat !== latitude) {
    marker.setLngLat([longitude, latitude]);
  }
  if (marker.isDraggable() !== draggable) {
    marker.setDraggable(draggable);
  }

  const currentOffset = marker.getOffset();
  const newOffset = markerOptions.offset ?? [0, 0];
  const [newOffsetX, newOffsetY] = Array.isArray(newOffset)
    ? newOffset
    : [newOffset.x, newOffset.y];
  if (currentOffset.x !== newOffsetX || currentOffset.y !== newOffsetY) {
    marker.setOffset(newOffset);
  }

  if (marker.getRotation() !== markerOptions.rotation) {
    marker.setRotation(markerOptions.rotation ?? 0);
  }
  if (marker.getRotationAlignment() !== markerOptions.rotationAlignment) {
    marker.setRotationAlignment(markerOptions.rotationAlignment ?? 'auto');
  }
  if (marker.getPitchAlignment() !== markerOptions.pitchAlignment) {
    marker.setPitchAlignment(markerOptions.pitchAlignment ?? 'auto');
  }

  return <MarkerContext.Provider value={{ marker, map }}>{children}</MarkerContext.Provider>;
}

type MarkerContentProps = {
  /** Custom marker content. Defaults to a blue dot if not provided */
  children?: ReactNode;
  /** Additional CSS classes for the marker container */
  className?: string;
};

function MarkerContent({ children, className }: MarkerContentProps) {
  const { marker } = useMarkerContext();

  return createPortal(
    <div className={cn('relative cursor-pointer', className)}>
      {children || <DefaultMarkerIcon />}
    </div>,
    marker.getElement(),
  );
}

function DefaultMarkerIcon() {
  return (
    <div className="relative w-4 h-4 bg-blue-9 border-2 border-mauve-1 rounded-full shadow-lg" />
  );
}

type MarkerPopupProps = {
  /** Popup content */
  children: ReactNode;
  /** Additional CSS classes for the popup container */
  className?: string;
  /** Show a close button in the popup (default: false) */
  closeButton?: boolean;
} & Omit<PopupOptions, 'className' | 'closeButton'>;

function MarkerPopup({
  children,
  className,
  closeButton = false,
  ...popupOptions
}: MarkerPopupProps) {
  const { marker, map } = useMarkerContext();
  const container = useMemo(() => document.createElement('div'), []);
  const prevPopupOptions = useRef(popupOptions);

  const popup = useMemo(() => {
    const popupInstance = new MapLibreGL.Popup({
      offset: 16,
      ...popupOptions,
      closeButton: false,
    })
      .setMaxWidth('none')
      .setDOMContent(container);

    return popupInstance;
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  useEffect(() => {
    if (!map) return;

    popup.setDOMContent(container);
    marker.setPopup(popup);

    return () => {
      marker.setPopup(null);
    };
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [map]);

  if (popup.isOpen()) {
    const prev = prevPopupOptions.current;

    if (prev.offset !== popupOptions.offset) {
      popup.setOffset(popupOptions.offset ?? 16);
    }
    if (prev.maxWidth !== popupOptions.maxWidth && popupOptions.maxWidth) {
      popup.setMaxWidth(popupOptions.maxWidth ?? 'none');
    }

    prevPopupOptions.current = popupOptions;
  }

  const handleClose = () => popup.remove();

  return createPortal(
    <div
      className={cn(
        'relative rounded-md border border-mauve-6 bg-mauve-1 p-3 text-mauve-12 shadow-md animate-in fade-in-0 zoom-in-95',
        className,
      )}
    >
      {closeButton && (
        <button
          type="button"
          onClick={handleClose}
          className="absolute z-10 transition-opacity rounded-sm top-1 right-1 opacity-70 ring-offset-mauve-1 hover:opacity-100 focus:outline-none focus:ring-2 focus:ring-blue-7 focus:ring-offset-2"
          aria-label="Close popup"
        >
          <X className="w-4 h-4" />
          <span className="sr-only">Close</span>
        </button>
      )}
      {children}
    </div>,
    container,
  );
}

type MarkerTooltipProps = {
  /** Tooltip content */
  children: ReactNode;
  /** Additional CSS classes for the tooltip container */
  className?: string;
} & Omit<PopupOptions, 'className' | 'closeButton' | 'closeOnClick'>;

function MarkerTooltip({ children, className, ...popupOptions }: MarkerTooltipProps) {
  const { marker, map } = useMarkerContext();
  const container = useMemo(() => document.createElement('div'), []);
  const prevTooltipOptions = useRef(popupOptions);

  const tooltip = useMemo(() => {
    const tooltipInstance = new MapLibreGL.Popup({
      offset: 16,
      ...popupOptions,
      closeOnClick: true,
      closeButton: false,
    }).setMaxWidth('none');

    return tooltipInstance;
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  useEffect(() => {
    if (!map) return;

    tooltip.setDOMContent(container);

    const handleMouseEnter = () => {
      tooltip.setLngLat(marker.getLngLat()).addTo(map);
    };
    const handleMouseLeave = () => tooltip.remove();

    marker.getElement()?.addEventListener('mouseenter', handleMouseEnter);
    marker.getElement()?.addEventListener('mouseleave', handleMouseLeave);

    return () => {
      marker.getElement()?.removeEventListener('mouseenter', handleMouseEnter);
      marker.getElement()?.removeEventListener('mouseleave', handleMouseLeave);
      tooltip.remove();
    };
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [map]);

  if (tooltip.isOpen()) {
    const prev = prevTooltipOptions.current;

    if (prev.offset !== popupOptions.offset) {
      tooltip.setOffset(popupOptions.offset ?? 16);
    }
    if (prev.maxWidth !== popupOptions.maxWidth && popupOptions.maxWidth) {
      tooltip.setMaxWidth(popupOptions.maxWidth ?? 'none');
    }

    prevTooltipOptions.current = popupOptions;
  }

  return createPortal(
    <div
      className={cn(
        'rounded-md bg-mauve-12 px-2 py-1 text-xs text-mauve-1 shadow-md animate-in fade-in-0 zoom-in-95',
        className,
      )}
    >
      {children}
    </div>,
    container,
  );
}

type MarkerLabelProps = {
  /** Label text content */
  children: ReactNode;
  /** Additional CSS classes for the label */
  className?: string;
  /** Position of the label relative to the marker (default: "top") */
  position?: 'top' | 'bottom';
};

function MarkerLabel({ children, className, position = 'top' }: MarkerLabelProps) {
  const positionClasses = {
    top: 'bottom-full mb-1',
    bottom: 'top-full mt-1',
  };

  return (
    <div
      className={cn(
        'absolute left-1/2 -translate-x-1/2 whitespace-nowrap',
        'text-[10px] font-medium text-mauve-12',
        positionClasses[position],
        className,
      )}
    >
      {children}
    </div>
  );
}

type MapControlsProps = {
  /** Position of the controls on the map (default: "bottom-right") */
  position?: 'top-left' | 'top-right' | 'bottom-left' | 'bottom-right';
  /** Show zoom in/out buttons (default: true) */
  showZoom?: boolean;
  /** Show compass button to reset bearing (default: false) */
  showCompass?: boolean;
  /** Show locate button to find user's location (default: false) */
  showLocate?: boolean;
  /** Show fullscreen toggle button (default: false) */
  showFullscreen?: boolean;
  /** Additional CSS classes for the controls container */
  className?: string;
  /** Callback with user coordinates when located */
  onLocate?: (coords: { longitude: number; latitude: number }) => void;
};

const positionClasses = {
  'top-left': 'top-2 left-2',
  'top-right': 'top-2 right-2',
  'bottom-left': 'bottom-2 left-2',
  'bottom-right': 'bottom-10 right-2',
};

function ControlGroup({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex flex-col rounded-md border border-mauve-6 bg-mauve-1 shadow-sm overflow-hidden [&>button:not(:last-child)]:border-b [&>button:not(:last-child)]:border-mauve-6">
      {children}
    </div>
  );
}

function ControlButton({
  onClick,
  label,
  children,
  disabled = false,
}: {
  onClick: () => void;
  label: string;
  children: React.ReactNode;
  disabled?: boolean;
}) {
  return (
    <button
      onClick={onClick}
      aria-label={label}
      type="button"
      className={cn(
        'flex items-center justify-center size-8 hover:bg-mauve-4 transition-colors',
        disabled && 'opacity-50 pointer-events-none cursor-not-allowed',
      )}
      disabled={disabled}
    >
      {children}
    </button>
  );
}

function MapControls({
  position = 'bottom-right',
  showZoom = true,
  showCompass = false,
  showLocate = false,
  showFullscreen = false,
  className,
  onLocate,
}: MapControlsProps) {
  const { map, isLoaded } = useMap();
  const [waitingForLocation, setWaitingForLocation] = useState(false);

  const handleZoomIn = useCallback(() => {
    map?.zoomTo(map.getZoom() + 1, { duration: 300 });
  }, [map]);

  const handleZoomOut = useCallback(() => {
    map?.zoomTo(map.getZoom() - 1, { duration: 300 });
  }, [map]);

  const handleResetBearing = useCallback(() => {
    map?.resetNorthPitch({ duration: 300 });
  }, [map]);

  const handleLocate = useCallback(() => {
    setWaitingForLocation(true);
    if ('geolocation' in navigator) {
      navigator.geolocation.getCurrentPosition(
        (pos) => {
          const coords = {
            longitude: pos.coords.longitude,
            latitude: pos.coords.latitude,
          };
          map?.flyTo({
            center: [coords.longitude, coords.latitude],
            zoom: 14,
            duration: 1500,
          });
          onLocate?.(coords);
          setWaitingForLocation(false);
        },
        (error) => {
          console.error('Error getting location:', error);
          setWaitingForLocation(false);
        },
      );
    }
  }, [map, onLocate]);

  const handleFullscreen = useCallback(() => {
    const container = map?.getContainer();
    if (!container) return;
    if (document.fullscreenElement) {
      document.exitFullscreen();
    } else {
      container.requestFullscreen();
    }
  }, [map]);

  if (!isLoaded) return null;

  return (
    <div
      className={cn('absolute z-10 flex flex-col gap-1.5', positionClasses[position], className)}
    >
      {showZoom && (
        <ControlGroup>
          <ControlButton onClick={handleZoomIn} label="Zoom in">
            <Plus className="size-4" />
          </ControlButton>
          <ControlButton onClick={handleZoomOut} label="Zoom out">
            <Minus className="size-4" />
          </ControlButton>
        </ControlGroup>
      )}
      {showCompass && (
        <ControlGroup>
          <CompassButton onClick={handleResetBearing} />
        </ControlGroup>
      )}
      {showLocate && (
        <ControlGroup>
          <ControlButton
            onClick={handleLocate}
            label="Find my location"
            disabled={waitingForLocation}
          >
            {waitingForLocation ? (
              <Loader2 className="size-4 animate-spin" />
            ) : (
              <Locate className="size-4" />
            )}
          </ControlButton>
        </ControlGroup>
      )}
      {showFullscreen && (
        <ControlGroup>
          <ControlButton onClick={handleFullscreen} label="Toggle fullscreen">
            <Maximize className="size-4" />
          </ControlButton>
        </ControlGroup>
      )}
    </div>
  );
}

function CompassButton({ onClick }: { onClick: () => void }) {
  const { isLoaded, map } = useMap();
  const compassRef = useRef<SVGSVGElement>(null);

  useEffect(() => {
    if (!isLoaded || !map || !compassRef.current) return;

    const compass = compassRef.current;

    const updateRotation = () => {
      const bearing = map.getBearing();
      const pitch = map.getPitch();
      compass.style.transform = `rotateX(${pitch}deg) rotateZ(${-bearing}deg)`;
    };

    map.on('rotate', updateRotation);
    map.on('pitch', updateRotation);
    updateRotation();

    return () => {
      map.off('rotate', updateRotation);
      map.off('pitch', updateRotation);
    };
  }, [isLoaded, map]);

  return (
    <ControlButton onClick={onClick} label="Reset bearing to north">
      <svg
        ref={compassRef}
        viewBox="0 24"
        className="transition-transform duration-200 size-5"
        style={{ transformStyle: 'preserve-3d' }}
      >
        <path d="M12 2L16 12H12V2Z" className="fill-red-500" />
        <path d="M12 2L8 12H12V2Z" className="fill-red-300" />
        <path d="M12 22L16 12H12V22Z" className="fill-muted-foreground/60" />
        <path d="M12 22L8 12H12V22Z" className="fill-muted-foreground/30" />
      </svg>
    </ControlButton>
  );
}

type MapPopupProps = {
  /** Longitude coordinate for popup position */
  longitude: number;
  /** Latitude coordinate for popup position */
  latitude: number;
  /** Callback when popup is closed */
  onClose?: () => void;
  /** Popup content */
  children: ReactNode;
  /** Additional CSS classes for the popup container */
  className?: string;
  /** Show a close button in the popup (default: false) */
  closeButton?: boolean;
} & Omit<PopupOptions, 'className' | 'closeButton'>;

function MapPopup({
  longitude,
  latitude,
  onClose,
  children,
  className,
  closeButton = false,
  ...popupOptions
}: MapPopupProps) {
  const { map } = useMap();
  const popupOptionsRef = useRef(popupOptions);
  const container = useMemo(() => document.createElement('div'), []);

  const popup = useMemo(() => {
    const popupInstance = new MapLibreGL.Popup({
      offset: 16,
      ...popupOptions,
      closeButton: false,
    })
      .setMaxWidth('none')
      .setLngLat([longitude, latitude]);

    return popupInstance;
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  useEffect(() => {
    if (!map) return;

    const onCloseProp = () => onClose?.();
    popup.on('close', onCloseProp);

    popup.setDOMContent(container);
    popup.addTo(map);

    return () => {
      popup.off('close', onCloseProp);
      if (popup.isOpen()) {
        popup.remove();
      }
    };
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [map]);

  if (popup.isOpen()) {
    const prev = popupOptionsRef.current;

    if (popup.getLngLat().lng !== longitude || popup.getLngLat().lat !== latitude) {
      popup.setLngLat([longitude, latitude]);
    }

    if (prev.offset !== popupOptions.offset) {
      popup.setOffset(popupOptions.offset ?? 16);
    }
    if (prev.maxWidth !== popupOptions.maxWidth && popupOptions.maxWidth) {
      popup.setMaxWidth(popupOptions.maxWidth ?? 'none');
    }
    popupOptionsRef.current = popupOptions;
  }

  const handleClose = () => {
    popup.remove();
    onClose?.();
  };

  return createPortal(
    <div
      className={cn(
        'relative rounded-md border border-mauve-6 bg-mauve-1 p-3 text-mauve-12 shadow-md animate-in fade-in-0 zoom-in-95',
        className,
      )}
    >
      {closeButton && (
        <button
          type="button"
          onClick={handleClose}
          className="absolute z-10 transition-opacity rounded-sm top-1 right-1 opacity-70 ring-offset-mauve-1 hover:opacity-100 focus:outline-none focus:ring-2 focus:ring-blue-7 focus:ring-offset-2"
          aria-label="Close popup"
        >
          <X className="w-4 h-4" />
          <span className="sr-only">Close</span>
        </button>
      )}
      {children}
    </div>,
    container,
  );
}

type MapRouteProps = {
  /** Optional unique identifier for the route layer */
  id?: string;
  /** Array of [longitude, latitude] coordinate pairs defining the route */
  coordinates: [number, number][];
  /** Line color as CSS color value (default: "#4285F4") */
  color?: string;
  /** Line width in pixels (default: 3) */
  width?: number;
  /** Line opacity from 0 to 1 (default: 0.8) */
  opacity?: number;
  /** Dash pattern [dash length, gap length] for dashed lines */
  dashArray?: [number, number];
  /** Callback when the route line is clicked */
  onClick?: () => void;
  /** Callback when mouse enters the route line */
  onMouseEnter?: () => void;
  /** Callback when mouse leaves the route line */
  onMouseLeave?: () => void;
  /** Whether the route is interactive - shows pointer cursor on hover (default: true) */
  interactive?: boolean;
};

function MapRoute({
  id,
  coordinates,
  color = '#4285F4',
  width = 3,
  opacity = 0.8,
  dashArray,
  onClick,
  onMouseEnter,
  onMouseLeave,
  interactive = true,
}: MapRouteProps) {
  const { map, isLoaded } = useMap();
  const autoId = useId();
  const sourceId = id ?? `route-source-${autoId}`;
  const layerId = id ?? `route-layer-${autoId}`;

  // Add source and layer on mount
  useEffect(() => {
    if (!isLoaded || !map) return;

    map.addSource(sourceId, {
      type: 'geojson',
      data: {
        type: 'Feature',
        properties: {},
        geometry: { type: 'LineString', coordinates: [] },
      },
    });

    map.addLayer({
      id: layerId,
      type: 'line',
      source: sourceId,
      layout: { 'line-join': 'round', 'line-cap': 'round' },
      paint: {
        'line-color': color,
        'line-width': width,
        'line-opacity': opacity,
        ...(dashArray && { 'line-dasharray': dashArray }),
      },
    });

    return () => {
      try {
        if (map.getLayer(layerId)) map.removeLayer(layerId);
        if (map.getSource(sourceId)) map.removeSource(sourceId);
      } catch {
        // ignore
      }
    };
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [isLoaded, map]);

  // When coordinates change, update the source data
  useEffect(() => {
    if (!isLoaded || !map || coordinates.length < 2) return;

    const source = map.getSource(sourceId) as MapLibreGL.GeoJSONSource;
    if (source) {
      source.setData({
        type: 'Feature',
        properties: {},
        geometry: { type: 'LineString', coordinates },
      });
    }
  }, [isLoaded, map, coordinates, sourceId]);

  useEffect(() => {
    if (!isLoaded || !map || !map.getLayer(layerId)) return;

    map.setPaintProperty(layerId, 'line-color', color);
    map.setPaintProperty(layerId, 'line-width', width);
    map.setPaintProperty(layerId, 'line-opacity', opacity);
    if (dashArray) {
      map.setPaintProperty(layerId, 'line-dasharray', dashArray);
    }
  }, [isLoaded, map, layerId, color, width, opacity, dashArray]);

  // Handle click and hover events
  useEffect(() => {
    if (!isLoaded || !map || !interactive) return;

    const handleClick = () => {
      onClick?.();
    };
    const handleMouseEnter = () => {
      map.getCanvas().style.cursor = 'pointer';
      onMouseEnter?.();
    };
    const handleMouseLeave = () => {
      map.getCanvas().style.cursor = '';
      onMouseLeave?.();
    };

    map.on('click', layerId, handleClick);
    map.on('mouseenter', layerId, handleMouseEnter);
    map.on('mouseleave', layerId, handleMouseLeave);

    return () => {
      map.off('click', layerId, handleClick);
      map.off('mouseenter', layerId, handleMouseEnter);
      map.off('mouseleave', layerId, handleMouseLeave);
    };
  }, [isLoaded, map, layerId, onClick, onMouseEnter, onMouseLeave, interactive]);

  return null;
}

type MapClusterLayerProps<P extends GeoJSON.GeoJsonProperties = GeoJSON.GeoJsonProperties> = {
  /** GeoJSON FeatureCollection data or URL to fetch GeoJSON from */
  data: string | GeoJSON.FeatureCollection<GeoJSON.Point, P>;
  /** Maximum zoom level to cluster points on (default: 14) */
  clusterMaxZoom?: number;
  /** Radius of each cluster when clustering points in pixels (default: 50) */
  clusterRadius?: number;
  /** Colors for cluster circles: [small, medium, large] based on point count (default: ["#51bbd6", "#f1f075", "#f28cb1"]) */
  clusterColors?: [string, string, string];
  /** Point count thresholds for color/size steps: [medium, large] (default: [100, 750]) */
  clusterThresholds?: [number, number];
  /** Color for unclustered individual points (default: "#3b82f6") */
  pointColor?: string;
  /** Callback when an unclustered point is clicked */
  onPointClick?: (
    feature: GeoJSON.Feature<GeoJSON.Point, P>,
    coordinates: [number, number],
  ) => void;
  /** Callback when a cluster is clicked. If not provided, zooms into the cluster */
  onClusterClick?: (clusterId: number, coordinates: [number, number], pointCount: number) => void;
};

function MapClusterLayer<P extends GeoJSON.GeoJsonProperties = GeoJSON.GeoJsonProperties>({
  data,
  clusterMaxZoom = 14,
  clusterRadius = 50,
  clusterColors = ['#51bbd6', '#f1f075', '#f28cb1'],
  clusterThresholds = [100, 750],
  pointColor = '#3b82f6',
  onPointClick,
  onClusterClick,
}: MapClusterLayerProps<P>) {
  const { map, isLoaded } = useMap();
  const id = useId();
  const sourceId = `cluster-source-${id}`;
  const clusterLayerId = `clusters-${id}`;
  const clusterCountLayerId = `cluster-count-${id}`;
  const unclusteredLayerId = `unclustered-point-${id}`;

  const stylePropsRef = useRef({
    clusterColors,
    clusterThresholds,
    pointColor,
  });

  // Add source and layers on mount
  useEffect(() => {
    if (!isLoaded || !map) return;

    // Add clustered GeoJSON source
    map.addSource(sourceId, {
      type: 'geojson',
      data,
      cluster: true,
      clusterMaxZoom,
      clusterRadius,
    });

    // Add cluster circles layer
    map.addLayer({
      id: clusterLayerId,
      type: 'circle',
      source: sourceId,
      filter: ['has', 'point_count'],
      paint: {
        'circle-color': [
          'step',
          ['get', 'point_count'],
          clusterColors[0],
          clusterThresholds[0],
          clusterColors[1],
          clusterThresholds[1],
          clusterColors[2],
        ],
        'circle-radius': [
          'step',
          ['get', 'point_count'],
          20,
          clusterThresholds[0],
          30,
          clusterThresholds[1],
          40,
        ],
      },
    });

    // Add cluster count text layer
    map.addLayer({
      id: clusterCountLayerId,
      type: 'symbol',
      source: sourceId,
      filter: ['has', 'point_count'],
      layout: {
        'text-field': '{point_count_abbreviated}',
        'text-size': 12,
      },
      paint: {
        'text-color': '#fff',
      },
    });

    // Add unclustered point layer
    map.addLayer({
      id: unclusteredLayerId,
      type: 'circle',
      source: sourceId,
      filter: ['!', ['has', 'point_count']],
      paint: {
        'circle-color': pointColor,
        'circle-radius': 6,
      },
    });

    return () => {
      try {
        if (map.getLayer(clusterCountLayerId)) map.removeLayer(clusterCountLayerId);
        if (map.getLayer(unclusteredLayerId)) map.removeLayer(unclusteredLayerId);
        if (map.getLayer(clusterLayerId)) map.removeLayer(clusterLayerId);
        if (map.getSource(sourceId)) map.removeSource(sourceId);
      } catch {
        // ignore
      }
    };
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [isLoaded, map, sourceId]);

  // Update source data when data prop changes (only for non-URL data)
  useEffect(() => {
    if (!isLoaded || !map || typeof data === 'string') return;

    const source = map.getSource(sourceId) as MapLibreGL.GeoJSONSource;
    if (source) {
      source.setData(data);
    }
  }, [isLoaded, map, data, sourceId]);

  // Update layer styles when props change
  useEffect(() => {
    if (!isLoaded || !map) return;

    const prev = stylePropsRef.current;
    const colorsChanged =
      prev.clusterColors !== clusterColors || prev.clusterThresholds !== clusterThresholds;

    // Update cluster layer colors and sizes
    if (map.getLayer(clusterLayerId) && colorsChanged) {
      map.setPaintProperty(clusterLayerId, 'circle-color', [
        'step',
        ['get', 'point_count'],
        clusterColors[0],
        clusterThresholds[0],
        clusterColors[1],
        clusterThresholds[1],
        clusterColors[2],
      ]);
      map.setPaintProperty(clusterLayerId, 'circle-radius', [
        'step',
        ['get', 'point_count'],
        20,
        clusterThresholds[0],
        30,
        clusterThresholds[1],
        40,
      ]);
    }

    // Update unclustered point layer color
    if (map.getLayer(unclusteredLayerId) && prev.pointColor !== pointColor) {
      map.setPaintProperty(unclusteredLayerId, 'circle-color', pointColor);
    }

    stylePropsRef.current = { clusterColors, clusterThresholds, pointColor };
  }, [
    isLoaded,
    map,
    clusterLayerId,
    unclusteredLayerId,
    clusterColors,
    clusterThresholds,
    pointColor,
  ]);

  // Handle click events
  useEffect(() => {
    if (!isLoaded || !map) return;

    // Cluster click handler - zoom into cluster
    const handleClusterClick = async (
      e: MapLibreGL.MapMouseEvent & {
        features?: MapLibreGL.MapGeoJSONFeature[];
      },
    ) => {
      const features = map.queryRenderedFeatures(e.point, {
        layers: [clusterLayerId],
      });
      if (!features.length) return;

      const feature = features[0];
      const clusterId = feature.properties?.cluster_id as number;
      const pointCount = feature.properties?.point_count as number;
      const coordinates = (feature.geometry as GeoJSON.Point).coordinates as [number, number];

      if (onClusterClick) {
        onClusterClick(clusterId, coordinates, pointCount);
      } else {
        // Default behavior: zoom to cluster expansion zoom
        const source = map.getSource(sourceId) as MapLibreGL.GeoJSONSource;
        const zoom = await source.getClusterExpansionZoom(clusterId);
        map.easeTo({
          center: coordinates,
          zoom,
        });
      }
    };

    // Unclustered point click handler
    const handlePointClick = (
      e: MapLibreGL.MapMouseEvent & {
        features?: MapLibreGL.MapGeoJSONFeature[];
      },
    ) => {
      if (!onPointClick || !e.features?.length) return;

      const feature = e.features[0];
      const coordinates = (feature.geometry as GeoJSON.Point).coordinates.slice() as [
        number,
        number,
      ];

      // Handle world copies
      while (Math.abs(e.lngLat.lng - coordinates[0]) > 180) {
        coordinates[0] += e.lngLat.lng > coordinates[0] ? 360 : -360;
      }

      onPointClick(feature as unknown as GeoJSON.Feature<GeoJSON.Point, P>, coordinates);
    };

    // Cursor style handlers
    const handleMouseEnterCluster = () => {
      map.getCanvas().style.cursor = 'pointer';
    };
    const handleMouseLeaveCluster = () => {
      map.getCanvas().style.cursor = '';
    };
    const handleMouseEnterPoint = () => {
      if (onPointClick) {
        map.getCanvas().style.cursor = 'pointer';
      }
    };
    const handleMouseLeavePoint = () => {
      map.getCanvas().style.cursor = '';
    };

    map.on('click', clusterLayerId, handleClusterClick);
    map.on('click', unclusteredLayerId, handlePointClick);
    map.on('mouseenter', clusterLayerId, handleMouseEnterCluster);
    map.on('mouseleave', clusterLayerId, handleMouseLeaveCluster);
    map.on('mouseenter', unclusteredLayerId, handleMouseEnterPoint);
    map.on('mouseleave', unclusteredLayerId, handleMouseLeavePoint);

    return () => {
      map.off('click', clusterLayerId, handleClusterClick);
      map.off('click', unclusteredLayerId, handlePointClick);
      map.off('mouseenter', clusterLayerId, handleMouseEnterCluster);
      map.off('mouseleave', clusterLayerId, handleMouseLeaveCluster);
      map.off('mouseenter', unclusteredLayerId, handleMouseEnterPoint);
      map.off('mouseleave', unclusteredLayerId, handleMouseLeavePoint);
    };
  }, [isLoaded, map, clusterLayerId, unclusteredLayerId, sourceId, onClusterClick, onPointClick]);

  return null;
}

export {
  MapRoot,
  useMap,
  MapMarker,
  MarkerContent,
  MarkerPopup,
  MarkerTooltip,
  MarkerLabel,
  MapPopup,
  MapControls,
  MapRoute,
  MapClusterLayer,
};
```

---

## 📂 `command.tsx`

Caminho original no repositório: `/packages/ui/src/components/command.tsx`

```tsx
import { Command as CommandPrimitive } from 'cmdk';
import type * as React from 'react';

import { cn } from '@manager/lib';
import { Check, SearchMd } from '@untitled-ui/icons-react';
import { Dialog, DialogContent, DialogDescription, DialogHeader, DialogTitle } from './dialog';
import { InputGroup, InputGroupAddon } from './input-group';

function Command({ className, ...props }: React.ComponentProps<typeof CommandPrimitive>) {
  return (
    <CommandPrimitive
      data-slot="command"
      className={cn(
        'bg-white text-zinc-950 rounded-xl! p-1 flex size-full flex-col overflow-hidden dark:bg-zinc-950 dark:text-zinc-50',
        className,
      )}
      {...props}
    />
  );
}

function CommandDialog({
  title = 'Command Palette',
  description = 'Search for a command to run...',
  children,
  className,
  showCloseButton = false,
  ...props
}: Omit<React.ComponentProps<typeof Dialog>, 'children'> & {
  title?: string;
  description?: string;
  className?: string;
  showCloseButton?: boolean;
  children: React.ReactNode;
}) {
  return (
    <Dialog {...props}>
      <DialogHeader className="sr-only">
        <DialogTitle>{title}</DialogTitle>
        <DialogDescription>{description}</DialogDescription>
      </DialogHeader>
      <DialogContent
        className={cn('rounded-xl! overflow-hidden p-0', className)}
        showCloseButton={showCloseButton}
      >
        {children}
      </DialogContent>
    </Dialog>
  );
}

function CommandInput({
  className,
  ...props
}: React.ComponentProps<typeof CommandPrimitive.Input>) {
  return (
    <div data-slot="command-input-wrapper" className="p-1 pb-0">
      <InputGroup className="bg-zinc-200/30 border-zinc-200/30 h-8! rounded-lg! shadow-none! *:data-[slot=input-group-addon]:pl-2! dark:bg-zinc-800/30 dark:border-zinc-800/30">
        <CommandPrimitive.Input
          data-slot="command-input"
          className={cn(
            'w-full text-sm outline-hidden disabled:cursor-not-allowed disabled:opacity-50',
            className,
          )}
          {...props}
        />
        <InputGroupAddon>
          <SearchMd className="size-4 shrink-0 opacity-50" />
        </InputGroupAddon>
      </InputGroup>
    </div>
  );
}

function CommandList({ className, ...props }: React.ComponentProps<typeof CommandPrimitive.List>) {
  return (
    <CommandPrimitive.List
      data-slot="command-list"
      className={cn(
        'no-scrollbar max-h-72 scroll-py-1 outline-none overflow-x-hidden overflow-y-auto',
        className,
      )}
      {...props}
    />
  );
}

function CommandEmpty({
  className,
  ...props
}: React.ComponentProps<typeof CommandPrimitive.Empty>) {
  return (
    <CommandPrimitive.Empty
      data-slot="command-empty"
      className={cn('py-6 text-center text-sm', className)}
      {...props}
    />
  );
}

function CommandGroup({
  className,
  ...props
}: React.ComponentProps<typeof CommandPrimitive.Group>) {
  return (
    <CommandPrimitive.Group
      data-slot="command-group"
      className={cn(
        'text-zinc-950 [&_[cmdk-group-heading]]:text-zinc-500 overflow-hidden p-1 [&_[cmdk-group-heading]]:px-2 [&_[cmdk-group-heading]]:py-1.5 [&_[cmdk-group-heading]]:text-xs [&_[cmdk-group-heading]]:font-medium dark:text-zinc-50 dark:[&_[cmdk-group-heading]]:text-zinc-400',
        className,
      )}
      {...props}
    />
  );
}

function CommandSeparator({
  className,
  ...props
}: React.ComponentProps<typeof CommandPrimitive.Separator>) {
  return (
    <CommandPrimitive.Separator
      data-slot="command-separator"
      className={cn('bg-zinc-200 -mx-1 h-px w-auto dark:bg-zinc-800', className)}
      {...props}
    />
  );
}

function CommandItem({
  className,
  children,
  ...props
}: React.ComponentProps<typeof CommandPrimitive.Item>) {
  return (
    <CommandPrimitive.Item
      data-slot="command-item"
      className={cn(
        "data-selected:bg-zinc-100 data-selected:text-zinc-950 data-selected:**:[svg]:text-zinc-950 relative flex cursor-default items-center gap-2 rounded-sm px-2 py-1.5 text-sm outline-hidden select-none [&_svg:not([class*='size-'])]:size-4 [[data-slot=dialog-content]_&]:rounded-lg! group/command-item data-[disabled=true]:pointer-events-none data-[disabled=true]:opacity-50 [&_svg]:pointer-events-none [&_svg]:shrink-0 dark:data-selected:bg-zinc-800 dark:data-selected:text-zinc-50 dark:data-selected:**:[svg]:text-zinc-50",
        className,
      )}
      {...props}
    >
      {children}
      <Check className="ml-auto opacity-0 group-has-[[data-slot=command-shortcut]]/command-item:hidden group-data-[checked=true]/command-item:opacity-100" />
    </CommandPrimitive.Item>
  );
}

function CommandShortcut({ className, ...props }: React.ComponentProps<'span'>) {
  return (
    <span
      data-slot="command-shortcut"
      className={cn(
        'text-zinc-500 group-data-selected/command-item:text-foreground ml-auto text-xs tracking-widest dark:text-zinc-400',
        className,
      )}
      {...props}
    />
  );
}

export {
  Command,
  CommandDialog,
  CommandInput,
  CommandList,
  CommandEmpty,
  CommandGroup,
  CommandItem,
  CommandShortcut,
  CommandSeparator,
};
```

---

## 📂 `item.tsx`

Caminho original no repositório: `/packages/ui/src/components/item.tsx`

```tsx
import { mergeProps } from '@base-ui/react/merge-props';
import { useRender } from '@base-ui/react/use-render';
import { type VariantProps, cva } from 'class-variance-authority';
import type * as React from 'react';

import { cn } from '@manager/lib';
import { Separator } from './separator';

function ItemGroup({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      role="list"
      data-slot="item-group"
      className={cn(
        'gap-4 has-[[data-size=sm]]:gap-2.5 has-[[data-size=xs]]:gap-2 group/item-group flex w-full flex-col',
        className,
      )}
      {...props}
    />
  );
}

function ItemSeparator({ className, ...props }: React.ComponentProps<typeof Separator>) {
  return (
    <Separator
      data-slot="item-separator"
      orientation="horizontal"
      className={cn('my-2', className)}
      {...props}
    />
  );
}

const itemVariants = cva(
  '[a]:hover:bg-zinc-100 rounded-md border border-zinc-200 text-sm w-full group/item focus-visible:border-zinc-950 focus-visible:ring-zinc-950/50 flex items-center flex-wrap outline-none transition-colors duration-100 focus-visible:ring-[3px] [a]:transition-colors dark:[a]:hover:bg-zinc-800 dark:border-zinc-800 dark:focus-visible:border-zinc-300 dark:focus-visible:ring-zinc-300/50',
  {
    variants: {
      variant: {
        default: 'border-transparent',
        outline: 'border-zinc-200 dark:border-zinc-800',
        muted: 'bg-zinc-100/50 border-transparent dark:bg-zinc-800/50',
      },
      size: {
        default: 'gap-3.5 px-4 py-3.5',
        sm: 'gap-2.5 px-3 py-2.5',
        xs: 'gap-2 px-2.5 py-2 [[data-slot=dropdown-menu-content]_&]:p-0',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'default',
    },
  },
);

function Item({
  className,
  variant = 'default',
  size = 'default',
  render,
  ...props
}: useRender.ComponentProps<'div'> & VariantProps<typeof itemVariants>) {
  return useRender({
    defaultTagName: 'div',
    props: mergeProps<'div'>(
      {
        className: cn(itemVariants({ variant, size, className })),
      },
      props,
    ),
    render,
    state: {
      slot: 'item',
      variant,
      size,
    },
  });
}

const itemMediaVariants = cva(
  'gap-2 group-has-[[data-slot=item-description]]/item:translate-y-0.5 group-has-[[data-slot=item-description]]/item:self-start flex shrink-0 items-center justify-center [&_svg]:pointer-events-none',
  {
    variants: {
      variant: {
        default: 'bg-transparent',
        icon: "[&_svg:not([class*='size-'])]:size-4",
        image:
          'size-10 overflow-hidden rounded-sm group-data-[size=sm]/item:size-8 group-data-[size=xs]/item:size-6 [&_img]:size-full [&_img]:object-cover',
      },
    },
    defaultVariants: {
      variant: 'default',
    },
  },
);

function ItemMedia({
  className,
  variant = 'default',
  ...props
}: React.ComponentProps<'div'> & VariantProps<typeof itemMediaVariants>) {
  return (
    <div
      data-slot="item-media"
      data-variant={variant}
      className={cn(itemMediaVariants({ variant, className }))}
      {...props}
    />
  );
}

function ItemContent({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="item-content"
      className={cn(
        'gap-1 group-data-[size=xs]/item:gap-0 flex flex-1 flex-col [&+[data-slot=item-content]]:flex-none',
        className,
      )}
      {...props}
    />
  );
}

function ItemTitle({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="item-title"
      className={cn(
        'gap-2 text-sm leading-snug font-medium underline-offset-4 line-clamp-1 flex w-fit items-center',
        className,
      )}
      {...props}
    />
  );
}

function ItemDescription({ className, ...props }: React.ComponentProps<'p'>) {
  return (
    <p
      data-slot="item-description"
      className={cn(
        'text-zinc-500 text-left text-sm leading-normal group-data-[size=xs]/item:text-xs [&>a:hover]:text-zinc-900 line-clamp-2 font-normal [&>a]:underline [&>a]:underline-offset-4 dark:text-zinc-400 dark:[&>a:hover]:text-zinc-50',
        className,
      )}
      {...props}
    />
  );
}

function ItemActions({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div data-slot="item-actions" className={cn('gap-2 flex items-center', className)} {...props} />
  );
}

function ItemHeader({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="item-header"
      className={cn('gap-2 flex basis-full items-center justify-between', className)}
      {...props}
    />
  );
}

function ItemFooter({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="item-footer"
      className={cn('gap-2 flex basis-full items-center justify-between', className)}
      {...props}
    />
  );
}

export {
  Item,
  ItemMedia,
  ItemContent,
  ItemActions,
  ItemGroup,
  ItemSeparator,
  ItemTitle,
  ItemDescription,
  ItemHeader,
  ItemFooter,
};
```

---

